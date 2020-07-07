---
description: >-
  Merged leaves is a simple techinique for Merkle tree rollup proof of massive
  items.
---

# Merged leaves

### Mission

Let's assume that what we're going to prove is the following merkle tree appending

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

### Problem

If we use a custom hash function to construct a merkle tree, it usually becomes very expensive in the EVM. we need to hash TREE\_DEPTH times and it becomes not possible to append lots of items at once.

### Solution

Therefore, we have to split them into number of chunks and use several transactions.

1. To add a massive number of items, we split the transactions and record the intermediate proof result.
2. To ensure the sequence of the item appending, every item will be merged sequentially into the `mergedLeaves` value.

   In the end, the result stored on the EVM will be

   ```text
    - start root
    - start index
    - result root
    - result index
    - mergedLeaves
   ```

   Let's see how it works. 

For example,

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

7. Using the stored proof, we can validate the following information is valid or not. The validation only returns the validity when the result of merging all items in the`itemsToAdd` is same with the `mergedLeaves` of the stored proof result.

   ```text
    startRoot: 0x0001234...,
    startIndex: 38,
    itemsToAdd: [0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09],
    resultRoot: 0xF1F3A4B,
    resultIndex: 47
   ```

    Here, we merge all items of the `itemsToAdd` with the following code.

   ```text
    merged = bytes32(0);
    for(uint i = 0; i < itemsToAdd.length; i++) {
        merged = keccak256(merged, itemsToAdd[i]);
    }
   ```

    Finally the `merged` value equals to the `mergedLeaves` value `0xDEFEDFED...` of the stored proof, it returns `true`, or will be reverted.

### Conclusion

1. Using this system, we can create a proof for the merkle tree roll up using multiple transactions.
2. It uses `keccak256` to check the exact sequence of the item appending.

[See the detail implementation](https://github.com/wilsonbeam/merkle-tree-rollup/blob/bc14f76f509c1d66e18686ad3f6bf3ed90b7f16f/contracts/library/OPRULib.sol#L40)

