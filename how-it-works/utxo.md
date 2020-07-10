# UTXO

UTXOs include the following properties.

| Property | Description |
| :--- | :--- |
| Ether | The amount of ETH |
| Public Key | A Babyjubjub point of the note owner |
| Salt | Random salt. Zkopru generates UTXO hash using this salt. |
| Token Address \(Optional\) | The address of the token contract when it includes ERC20 or ERC721. The default value is 0. |
| ERC20 Amount \(Optional\) | The amount of the ERC20 token when it includes ERC20. The default value is 0. |
| NFT Id \(Optional\) | The id of the ERC721 token when it includes ERC721. The default value is 0. |

And then Zkopru computes the leaf hash with Poseidon hash:

```javascript
var intermediate_hash = poseidon(ether, pub_key.x, pub_key.y, salt)
var result_hash = poseidon(intemediate_hash, token_address, erc20, nft)
```

