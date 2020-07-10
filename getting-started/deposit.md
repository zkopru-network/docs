# Deposit

Please select an account to use. Then it will print the account's detailed information on the infobox.

![](https://i.imgur.com/RcDK7wK.png)

In this example, the account has 100 ETH on the layer-1, and we will move this to the layer-2 using deposit menu.

1. Select "Deposit."
2. Select "Ether\(balance: 100ETH\)."
3. Type the amount of ETH.
4. Type the fee for the coordinator.
5. Confirm the deposit transaction.

Then the wallet stores the detail of your deposit leaf, and the leaf is recorded on the layer-1 contract. Then the contract merges your leaf into the staged "mass deposit." Subsequently, the coordinator commits the staged mass deposit when it proposes a new block. Alternatively, the coordinator can commit the staged mass deposit manually. Finally, the coordinator includes the committed mass deposits into the new proposal, and then you can use your UTXOs on the layer-2.

