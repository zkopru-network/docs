# Transaction

![zkopru transaction ](https://docs.google.com/drawings/d/e/2PACX-1vRDLDx-gJjKCmmFOXcVFt8suboQtKlUFWEU5y_hkaPYUOXx3iiyO3QJlDnfKw3meglNba3ZazPL55zN/pub?w=1448&h=1377)

Basically, a zk transaction accepts number of inputs as its inflow and creates number of outputs for its outflow. To validate a zk transaction, it should satisfy the followings:

## Inflow validation

Zkopru achieves the privacy using the commitment-nullifier scheme. It means that a zk transaction spends a UTXO while not revealing which note is been used. Instead, we reveal the nullifier which is derived from the UTXO but not possible to link with its original UTXO.

To spend the UTXOs, if should satisfy the follownig conditions:

### UTXO membership proof

The tx builder submits Merkle proofs of every utxo to prove its existence. For an effective SNARK computation, UTXO tree uses Poseidon for its hash function.
    
### Ownership proof

Only the owner should be possible to spend the UTXO. For this condition, every note has a public key field, a babyjubjub point. And using the paired private key, the owner can create an EdDSA signature to prove its ownership.

### Commitment proof

To calculate the total sum of the inflow, the circuit should have detail information of the input UTXOs. Therefore, the owner should provider the details and its poseidon hash should equal to the leaf hash of the Merkle proof and the ownership proof.

### Nullifier proof

The given nullifiers should be correctly derived from the input UTXOs 

## Outflow validation

A zk transaction can create 3 types of outputs: UTXO, Withdrawal and Migration. If the zk transaction creates UTXOs, they are appended to the UTXO tree. Or when it creates withdrawal outputs, Zkopru appends them to the withdrawal tree. Lastly, mass migration, which is a part of the layer 2 block, comprises the migration outputs of the block's every zk transaction.

Therefore, the outflow should satisfy the followings:

1. When the output is a UTXO type, the public hash value of the output is same with the calculated in the SNARK circuit.
2. If the type of output is withdrawal or migration, it reveals the detail of the note.

    The reason why it should reveal the details is because it should move the correct amount of assets to the outside of the network.

## Zero-sum proof

Finally, the zk transaction should guarantee that the inflow equals to the outflow including the fee.

