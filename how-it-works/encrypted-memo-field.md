# Encrypted memo field

A zk transaction can include 81 bytes of encrypted memo field for the recipient. Because of the zero-knowledge characteristics, even the recipient cannot know the reception without interactive process. Therefore, to keep the non-interactive way, we can put some encrypted data in the memo field for the recipient.

### Encryption

It generates the shared key using the Diffie-Hellman Key exchange protocol. The details steps to produce the shared key for the sender is:

1. Create an ephemeral key and its homomorphic hidden value.

   $$
   ephemeral = e\\
   public\_ephemeral = g^e
   $$

2. Mulitply its ephemeral key to the recipient's public key

   $$
   recipient\_pubkey = g^a\\
   shared\_key = (g^a)^e
   $$

3. Prepare miminum data to encrypt

   ```text
   data = {
       salt // 16 byte
       tokenId, // 1 byte
       value, // 32 byte
   }
   ```

4. Encrypt the data using chacha20 algorithm and create the memo data with the public ephemeral key

   ```text
   ephemeral = random.new()
   public_ephemeral = generator.multiply(ephemeral)
   shared_key = recipient_jubjub.multiply(ephemeral)
   cipher_text = chacha20.encrypt(data, shared_key)
   memo = public_ephemeral + cipher_text
   ```

### Decryption

Using the Diffie-Hellman key exchange protocol, the recipient also creates the shared key using the public ephemeral key and the private key.

1. Parse memo and get shared key

   ```text
   public_ephemeral, cipher_text = parse(memo)
   shared_key = public_ephemeral.multiply(private_key)
   ```

2. Decrypt the cipher text using the shared key

   ```text
   decrypted = cipher_text.decrypt(shared_key)
   ```

3. Using the decrypted result, recipient tries to make various possible UTXOs. It is because the encrypted data has only 49 bytes to minimize the calldata size. Therefore, the recipient should try various combinations to check whether the transaction includes the recovered UTXO hashes. If it fails to find the recovored UTXOs in the tx, it considers that the tx has no output for the recipient.

![Encryption &amp; decryption](../.gitbook/assets/encryption-and-decryption.png)

### Compressed data

To minimize the calldata, Zkopru compresses original data into 49 bytes data. First of all, it gets rid of the public key from the encryption candidate because the recipient will infer that using the own public key. And it uses the Token ID which maps the supported token addresses and the indexes from 0 to 255. And then because the `value` can be `ether`, `erc20Amount`, or `nftId`, the recipient creates 3 types of UTXOs for those 3 cases. Finally, if any of the inferred UTXO exists in the transaction's output list, the recipient successfully receives the UTXO.

### Limitation

Zkopru does not enforces the circuit to check the encryption protocol. Therefore, if the sender does not use an appropriate shared key or data, the recipient will not receive the note.

