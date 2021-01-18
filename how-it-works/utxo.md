# UTXO

UTXO's hash is computed by concatenating Zkopru account's public spending key, a salt, and data hash.

* Zkopru account's public spending key `S` is defined in [account](account.md) part.
* Salt is a 16 bytes(128 bit) number to add some randomness.
* Data hash is a poseidon hash of Ether amount, token address (default is 0), NFT id if the note contains NFT, and the oken amount if it contains ERC20.

Zkopru protocol supports combination types of notes such as Ether and ERC20 or Ether and NFT. But, we recommend to use Ether only note, NFT only note, or ERC20 only note since the encrypted memo field does not support the combination types.

```javascript
const S = poseidon(p*G, n) // public spending key. See how-it-works/account
const salt = random16BytesSalt() // salt
const asset = poseidon(eth, tokenAddr, erc20, nft) // asset hash
const utxoHash = poseidon(S, salt, asset)
```

Please note that the erc20 field and the nft field cannot have both non-zero value at the same time.
