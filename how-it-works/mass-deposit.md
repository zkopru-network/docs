# Mass deposit

### **What happens when a user deposits assets to Zkropu.**

1. Zkopru contract transfers the given amount of assets from the user's account to itself.
2. It verifies that the `note` has a valid hash of the given information.
3. It merges the note to the last item of the `MassDeposit[]` list

### **What is MassDeposit?**

MassDeposit is a single `mergedLeaves` bytes32 value to be used for roll up proof. Please check what is `mergedLeaves` for roll up proof [here](https://github.com/wilsonbeam/zk-optimistic-rollup/wiki/Merged-leaves-for-merkle-tree-roll-up-proof). And if a coordinator proposes a block which includes a MassDeposit, it means that the block is appending all of the notes in the MassDeposit to its UTXO merkle tree.

{% page-ref page="merged-leaves.md" %}

### **How does the coordinator handle MassDeposits?**

Coordinators can include only the "committed" MassDeposits that do not change. And to include a MassDeposit, the coordinator monitors the `Deposit` event. ZkOPRU contract emits `Deposit` event to let the coordinators know that a new deposit is merged into the n-th items of the `MassDeposits[]`.

### **When does a MassDeposit becomes "committed"**

It is important to push the deposits to the layer-2 as quickly as possible. Therefore, it freezes the latest MassDeposit when every new block is proposed by the coordinators.

### **Does the coordinator can include more than one MassDeposit?**

Yes, it is possible to include a number of MassDeposits at once in the range of maximum challengeable cost.

