# How it works

## Optimistic + RollUp + zk-transaction = zkopru

The "Optimistic" way in Ethereum means that we can skip the computation of a transaction by a challenge system if we provide enough data on-chain. Plasma Group brought up this concept to solve the data availability problem in the plasma solutions. 

This concept gives us a significant advantage in computation. Especially for the implementation of the zk-transaction on Ethereum, it is necessary to use a SNARK-friendly hash function to construct a Merkle tree, which is usually very expensive on EVM. However, using the optimistic rollup, we can update the SNARK friendly Merkle tree at a low cost compare to computing it on-chain. As a result, this protocol consumes about 8,800 gas per a private transaction.


Most of all, Zkopru can continuously upgrade itself because it supports mass migration which allows moving assets to the other layer 2 network.

