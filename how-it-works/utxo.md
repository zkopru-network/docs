# UTXO

UTXOs include the following properties.

| Property | Description |
| :--- | :--- |
| Zk public key | `= poseidon(jubjub_pubkey.x, jubjub_pubkey.y, nullifier_seed)` |
| Salt | Randomly generated salt value |
| Ether | The amount of ETH |
| Token Address \(Optional\) | The address of the token contract when it includes ERC20 or ERC721. Default value is 0. |
| ERC20 Amount \(Optional\) | The amount of the ERC20 token when it includes ERC20. Default value is 0. |
| NFT Id \(Optional\) | The id of the ERC721 token when it includes ERC721. Default value is 0. |

And then Zkopru computes the leaf hash with Poseidon hash:

```javascript
var utxo_hash = poseidon(zk_pub_key, salt, ether, token_address, erc20, nft)
```

