# Grove: Merkle trees of zkopru

Zkopru's grove consists of UTXO trees, nullifier tree, and withdrawal trees.

UTXO trees are append-only usage Merkle trees containing UTXOs. Users can spend the UTXOs as the inflow of the transaction by submitting the inclusion Merkle proof. And the output results of transactions are appended back into the latest UTXO tree.

Also, if the zk-transaction create withdrawal outputs, Zkopru appends them into the latest withdrawal tree. Once the tree's root is marked as finalized, the owner can withdraw assets by proving the ownership

Afterwards, by the commitment-nullifier scheme, the nullifier of the spent UTXOs are marked as used in the nullifier tree, a unique sparse Merkle tree. If a transaction tries to use an already nullifiered leaf, it becomes invalidated and the block proposer gets slashed by the challenge system.


![grove](https://docs.google.com/drawings/d/e/2PACX-1vTXH2aQr0HZmWZbbA2ENOISW5hT5XsG2dda90RBSaQH-2ClqlZvFKlSTppR38b2ixUbqQeMOTztC-LA/pub?w=1031&h=564)

## Specification of Merkle trees

|       | UTXO Tree | Nullifier Tree | Withdrawal Tree |
| ----- | --------- | -------------- | --------- |
| Type | Sparse Merkle Tree | Sparse Merkle Tree | Sparse Merkle Tree |
| Depth | 31        | 256            | 31        |
| Hash  | Poseidon  | Keccak256      | Keccak256 |
| How to update | append only with sub-tree rollup with 5 depth tree | SMT roll-up | append only with sub-tree rollup with 5 depth tree
| Cost (gas/leaf) | 180k | 351k | 5.2k |

## How to manage the UTXO Trees

A single UTXO tree is a sparse merkle tree for the membership proof. It uses Poseidon hash, one of the cheapest hash function inside the SNARK, to generate a zk SNARK proof to hide the spending hash and its path.

To append new leaves to the UTXO tree, the coordinator performs the following steps.
1. Prepare an array.
2. Coordinator picks MassDeposits to include, and append every deposit in the MassDeposits into the array.
3. L2 transactions generates new UTXOs. Append newly generated UTXOs to the array.
4.  Split the prepared array with the chunk size 32.
5.  Construct sub trees and perform the sub-tree rollup.

If the UTXO tree is fully filled with (2^31) items, the system archive the fully filled tree and start a new tree. The archived trees are also allowed to be used for the reference for the inclusion proofs of the transactions.

The root is updated by the optimistic way and is verified when only there exist a challenge. And for the challenge, it generates an on-chain fraud-proof using the sub-tree rollup methodology. Sub-tree rollup appends fixed size of sub trees instead of appending items 1 by 1. When if the sub-tree's depth is 5, it'll append 32 items at once. If it contains only 18 items, the remaining 14 items will be remained forever as zeroes. This sub-tree rollup dramatically reduces the gas cost about 20 times compared to the roll up. To check the source code, please go to [contracts/controllers/Challengeable1.sol](https://github.com/wilsonbeam/zk-optimistic-rollup/blob/develop/contracts/controllers/Challengeable1.sol). And to see how the sub-tree works please go to [merkle-tree-rollup/contracts/library/SubTreeRollUpLib.sol](https://github.com/wilsonbeam/merkle-tree-rollup/blob/master/contracts/library/SubTreeRollUpLib.sol)

![sub-tree-rollup](https://docs.google.com/drawings/d/e/2PACX-1vRoBO2Nl-M8elgz55jzgxuux1f6FbrZIiUCeY3Z9Sf7haKF8kSP548L8pXcdh_VCfT_gcW1gywY4VKY/pub?w=959&h=417)

## Nullifier Tree

Every transfer, withdrawal, and migration transacion spends UTXOs with the inclusion proofs and marks the derived nullifiers as used on to the nullifier tree. Thus, the nullifier tree is a very big size sparse merkle tree to record every spent UTXOs in the 254-depth of Sparse Merkle Tree. Therefore, Zkopru uses keccak256, the cheapest hash function, for nullifier tree's hash function.

To update the nullifier tree, the cooridnator performs the following steps.

1. Pick transactions (transfer, withdrawal, migratoin) and collect all nullifiers from the transactions.
2. Check if any of the nullifier is already used.
3. Mark every nullifiers as used. And during the update process, if any of the nullifier does not make any change of the nullifier tree root, discard the transaction because it tries a double-spending.

And just like the UTXO tree, it updates the root with the optimistic way. If there's any problem we can prove a nullifier used more than twice by generating a fraud-proof on-chain. To see how it works please see [RollUpChallenge.sol](https://github.com/wanseob/zkopru/blob/master/packages/contracts/contracts/controllers/challenges/RollUpChallenge.sol) and [SMT.sol](https://github.com/wanseob/zkopru/blob/master/packages/contracts/contracts/libraries/SMT.sol).

## Withdrawal Tree

The only difference with the withdrawal tree and UTXO tree is that the withdrawal tree uses keccak256 for the hash function. The reason why it uses keccak256 is that Zkopru needs withdrawal tree's Merkle proof on the smart contract while it needs UTXO tree's Merkle proof inside the SNARK circuit. The leaves in the withdrawal trees become withdrawable on the layer 1 smart contract after its tree's root becomes finalized.

To update the withdrawal tree, the coordinator performs the following steps.

1. Collect every withdrawal leaf of the picked transactions.
2. Split the collected withdrawal array with the chunk size 32.
3. Construct sub trees and perform the sub-tree rollup.

