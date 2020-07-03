# UTXO

A UTXO includes the following properties.

| Property | Description |
| :--- | :--- |
| Ether | The amount of ETH |
| Public Key | A babyjubjub point of the note owner |
| Salt | Random salt. It is used to generate nullifier |
| Token Address \(Optional\) | The address of the token contract when it includes ERC20 or ERC721. Default value is 0. |
| ERC20 Amount \(Optional\) | The amount of the ERC20 token when it includes ERC20. Default value is 0. |
| NFT Id \(Optional\) | The id of the ERC721 token when it includes ERC721. Default value is 0. |

And then Zkopru computes the leaf hash with Poseidon hash:

```javascript
var intermediate_hash = poseidon(ether, pub_key.x, pub_key.y, salt)
var result_hash = poseidon(intemediate_hash, token_address, erc20, nft)
```

