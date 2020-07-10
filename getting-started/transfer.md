# Transfer

Once your deposit is included in the block, you can now use the private transfer feature.

![](https://i.imgur.com/R5hIKUM.png)

To send your ETH, ERC20, or ERC721, you need to get the recipient's Babyjubjub public key. The Babyjubjub public key is derived from the corresponding Ethereum account's private key.

1. Type the Babyjubjub public key of the recipient.

   > For testing, you can get your other account's public key from the infobox.

2. Type the amount of transfer.
3. Type the price per byte for the tx fee. Zkopru wallet fetches the fee per byte from the coordinator, and it is set to the default value. If you want to give more fees, you can type the custom value. Please note that the total fee depends on the size of your transaction.

   > This is because the calldata size mainly affects the cost of Optimistic Rollup. To test a tx instantly, you can just set the price about 100 times. Then the coordinator will include your transaction because it has aggregated enough fee for proposing.

4. Finally, it generates zk SNARK proof in a few seconds, and send the transaction to the coordinator.

   > It currently uses the HTTP protocol to send a transaction to the coordinator. To secure the network privacy, you should use Tor.

5. Once you have created a transaction, the UTXOs will be locked, which are used for the transaction.
6. After the coordinator includes the transaction, the wallet will mark the locked UTXOs as spent and apply the new UTXOs to your account's balance.

