# Token support

For the [memo field decryption process](encrypted-memo-field.md), we use 8 bits token Id field instead of the 160 bits full size token address. Therefore, some different tokens have a same token Id for their encryption/decryption processes.

For an efficient process of encrypting and decrypting, the token Id is determined by getting the modulus dividing the token address by 256(i.e. `id = tokenAddr (mod 256)`.)

And clients will try to encrypt or decrypt UTXOs using the token ids which are registered in the smart contract.

In more detail, when a sender tries to encrypt a UTXO which contains ERC20,

1. Make sure that the ERC20 token address is registered on the smart contract.
2. Calculate the token Id `tokenId = tokenAddr (mod 256)`.
3. Use the token Id for the encryption instead of putting the full token address.

Otherwise, the recipient will decrypt the memo field just like

1. Calculate the shared key using ECDH and parse the token Id from the decrypted cipher text.
2. Fetch the registered token addresses from the smart contract.
3. Get the token address candidates using the parsed token identifier(i.e. `filtered = registeredAddresses.filter(addr => addr.mod(256) === tokenId)`.)
4. Tries to construct the same utxo hash using the decrypted data and the token address candidates.
5. If the recipient succeed to construct the same utxo hash, then it is for the recipient or it is for another user.
