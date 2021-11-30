# Account

Zkopru account has 64 bytes address and is encoded with Base58Check algorithm. To understand how the Zkopru account works, you must know how the "viewing key" and the "spending key" work in Zkopru.

For compliance compatibility, Zkopru supports a viewing key system like ZCash. The [encrypted memo field](encrypted-memo-field.md) of a transaction includes private data that can decode the transaction details. Viewing key `n`, which is also a nullifier seed, is used to generate the public viewing key `N = n*G` where `G` is a constant generator EC point. With the public viewing key, the transaction sender makes an ephemeral shared key using ECDH, encrypts the transaction secrets, and puts them into the transaction's memo field. The latter 32 bytes of the total 64 bytes address stands for the public viewing key here.

The viewing key `n` also works as a seed value to generate the nullifiers to nullify the owner's UTXOs. Therefore, if Alice knows Bob's `n`, Alice can track the transactions by decoding the nullifiers and decrypting the memo field.

To prevent users from losing their keys, Zkopru derives a private spending key `p` from the Ethereum account's private key and then derive the nullifier seed from the `p`. And then, Zkopru uses `p` for EdDSA to verify the ownership of a UTXO to spend. Please note that a UTXO is a hash of `poseidon(S, data, salt)`. It means that Zkopru uses `S`, which contains `n`, for its commitment in the commitment-nullifier scheme.

![Zkopru account model](https://docs.google.com/drawings/d/e/2PACX-1vRev9wd5mB5aKuxAonSZMddKmsh5ysSe1ETl-iNJA8Ga7RiNTj0yRFjaGmDS53BLE6gqLailmdu4Z4t/pub?w=959&h=609)

| Key | Size | How to get | Where to use |
| :--- | :--- | :--- | :--- |
| Master seed | 32 bytes | Randomly generated | Recover keys |
| Ethereum private key | 32 bytes | Derived from the master seed with BIP39 | Layer1 ECDSA |
| Ethereum public address | 20 bytes | Derived from the Ethereum private key | Layer 1 interactions |
| `p`: Zkopru private spending key | 254 bits | Derived from the Ethereum private key | Layer2 EdDSA |
| `S`: Zkopru public spending key | 254 bits | Poseidon(p*G, n) | Commitment & ownership |
| `n`: Zkopru private viewing key & nullifier seed | 254 bit | Derived from the Zkopru private key | Viewing txs & creating nullifiers |
| `N`: Zkopru public viewing key | 32 bytes | N = n*G | Generating shared key(ECDH) |
| Zkopru public address | 64 bytes | Concatenation of `S` and `N` | Layer 2 interactions |
