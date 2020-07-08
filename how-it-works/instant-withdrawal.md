# Instant withdrawal

In Zkopru, withdrawers can request instant withdrawal by setting the instant withdrawal fee for each withdrawal note. Then, anyone can pay in advance for the unfinalized withdrawals and get the fee for them.

To request the instant withdrawal, the owner generates an ECDSA signature for her note and broadcast it. Then anyone, who has enough assets to pay, can pay in advance for the withdrawal using the signature. Once the transaction is successfully executed, then the ownership of the withdrawal note gets transferred to the payer. Finally, the prepayer withdraws them after the finalization.

#### References

{% embed url="https://github.com/wanseob/zkopru/issues/33" %}

