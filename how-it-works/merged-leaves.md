---
description: >-
  Merged leaves is used for item append sequence validation for the merkle tree
  rollup of massive items.
---

# Merged leaves & optimistic rollup

Merged leaves concept is used for the sequence verification of the appended items when we want to prove merkle tree updates through multiple transactions. Let's assume that what exactly we're going to prove is the following result.

```text
[Original merkle tree]
    - Root: 0xabcd...
    - Index: m
[Items to add]
    - [leaf_1, leaf_2, ..., leaf_n]
[Updated merkle tree]
    - Root: 0xfdec...
    - Index: m + n
```

To prove above, we will run the following steps:

1. To add a massive number of items, we split the transactions and record the intermediate proof result.
2. To ensure the sequence of the item appending, every item will be merged sequentially into the `mergedLeaves` value.

   ```text
   mergedLeaves = 0
   mergedLeaves = hash(mergedLeaves, items[0])
   mergedLeaves = hash(mergedLeaves, items[1])
   ...
   mergedLeaves = hash(mergedLeaves, items[n])
   ```

3. In the end, the result stored on the EVM will be

   ```text
    - start root
    - start index
    - result root
    - result index
    - mergedLeaves
   ```

   And then we can compare the merkle tree update result.

Let's see a more detail example.

* `startRoot` is `0x0001234...`
* `startIndex` is 38
* `itemsToAdd` is `[0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09]`
* we are now trying to prove `resultRoot` is `0xabcd1234...` when after adding all items in the `itemsToAdd` list.

We're going to add 3 items at once, so after 2 times of transactions, we can have a proof for the merkle tree transition on EVM. Note that I've used random values for the hash calculation for this example.

1. Start

   ```text
    [stored proof on the EVM]
    startRoot = 0x0001234...;
    startIndex = 38;
    resultRoot = 0x0001234...;
    resultIndex = 38;
    mergedLeaves = 0x0
   ```

2. Add the first item

   ```text
    [calculating...]
    startRoot = 0x0001234...;
    startIndex = 38;
    resultRoot = 0xAFCA1234...;
    resultIndex = 39;
    mergedLeaves = keccak256(0x0, 0x01) = 0xA0...
   ```

3. Add the second item

   ```text
    [calculating...]
    startRoot = 0x0001234...;
    startIndex = 38;
    resultRoot = 0xBA891234...;
    resultIndex = 40;
    mergedLeaves = keccak256(0xA0..., 0x02); = 0xB1...
   ```

4. Add the third item & record to the storage

   ```text
    [stored proof on the EVM]
    startRoot = 0x0001234...;
    startIndex = 38;
    resultRoot = 0xC9B31234...;
    resultIndex = 41;
    mergedLeaves = keccak256(0xB1..., 0x03); = 0xC3...
   ```

5. To add the fourth item, we will retrive the result from the storage and keep going to append items.
6. As a result we now have the result on Ethereum storage which proves the valid merkle tree transition by the EVM calculation.

   ```text
    [stored proof on the EVM]
    startRoot = 0x0001234...;
    startIndex = 38;
    resultRoot = 0xF1F3A4B...;
    resultIndex = 47;
    mergedLeaves = 0xDEFEDFED...;
   ```

7. Using the stored proof, we can validate the following information is valid or not. To validate the information, it computes the `mergedLeaves` result of the `itemsToAdd` and compare it with the stored `mergedLeaves`. 

   ```text
    startRoot: 0x0001234...,
    startIndex: 38,
    itemsToAdd: [0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09],
    resultRoot: 0xF1F3A4B,
    resultIndex: 47
   ```

    Here is how it computes the `mergedLeaves` of `itemsToAdd`

   ```text
    merged = bytes32(0);
    for(uint i = 0; i < itemsToAdd.length; i++) {
        merged = keccak256(merged, itemsToAdd[i]);
    }
   ```

   Finally, if the result `merged` value equals to the `mergedLeaves` value `0xDEFEDFED...` of the stored proof, it returns `true`, or will be reverted.

[See the detail implementation](https://github.com/wanseob/zkopru/blob/034ad7b41eca2a9fc0d344a5b5a8a4525e904c96/packages/contracts/contracts/libraries/Tree.sol#L155)

