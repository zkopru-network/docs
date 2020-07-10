# Account

A zkopru account manages both of the layer 1 and layer 2 key pairs. First of all, the account has an Ethereum account from a randomly generated private key. This is used for interactions on the layer 1. Second, Zkopru wallet creates a Babyjubjub private key and public key set from the Ethereum account's private key. This Babyjubjub keyset is used for the EdDSA and encrypted memo field on the layer 2.

| Key | Size | How to get | Where to use |
| :--- | :--- | :--- | :--- |
| Master seed | 32 byte | Randomly generated | Recover keys |
| Ethereum private key | 32 byte | Derived from master seed with BIP39 | Layer1 ECDSA |
| Ethereum public address | 20 byte | Derived from private key | Layer 1 interactions |
| Jubjub private key | 254 bit | Derived from Eth private key | Create EdDSA to spend UTXO |
| Jubjub public key | \(254 bit, 254 bit\) | Derived from jubjub private key | Proving ownership of a UTXO |



