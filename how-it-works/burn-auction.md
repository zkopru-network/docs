# Burn Auction

Zkopru uses a burn auction to determine who is allowed to propose blocks in the network. Coordinators must burn their Ether in exchange for the right to collect fees from proposed blocks. This Ether is collected by Zkopru with the intent of spending on public good services.

### Rounds

Each auction round lasts 40 blocks (10 minutes at 15 seconds/block). If no bids exist for the round anyone may propose new blocks. If the winning proposer fails to propose a block in the first half of the round anyone may propose a block for the remainder of the round.

### Bidding

Potential block proposers may bid on rounds to gain exclusive proposal rights. Each bid must be at least 10% higher than the previous bid. Rounds can be bid on 30 days in advance up until 2 rounds (80 blocks) before the start block.

### Urls

Block proposers should provide a string url that other coordinators can use to connect and forward transactions. The url field should be formatted as comma separated `host:port` combinations. For example:

```
Proposer 1:
192.168.1.199:8888,zkopru.mydomain.com:8888,1.0.0.1:8888

Proposer 2:
10.0.0.1:8888,zkopru.proposer2.com:2192,192.168.1.2

Proposer 3:
zkopru.proposer3.com:8888
```

Each of these are valid url strings. Each host/port combination will be tried first with `https` and then with `http`. The first host that sends an appropriate response will be used for communication. Supplying a valid `host:port` combination is important, by default coordinators will forward transactions to the url of the current round owner. Failing to supply a valid url means missing potential transaction fees!

### Refunds

If a coordinator is outbid for a round the losing bid is stored in the contract and may be refunded at any time.
