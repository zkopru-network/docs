# Mass deposit

### **What happens when a user deposits assets to Zkropu.**

1. Zkopru contract transfers the given amount of assets from the user's account to itself.
2. It verifies that the `note` has a valid hash of the given information.
3. It merges the note to the last item of the `MassDeposit[]` list

### **What is MassDeposit?**

MassDeposit is a single `mergedLeaves` bytes32 value to be used for rollup proof. Please check what is `mergedLeaves` for rollup proof [here](/how-it-works/merged-leaves.md). If the coordinator proposes a block including MassDeposits, the block appends all notes in the MassDeposit to its UTXO Merkle tree.

{% page-ref page="merged-leaves.md" %}

### **How does the coordinator handle MassDeposits?**

Coordinators can include only the "committed" MassDeposits that do not change anymore. To include a MassDeposit, the coordinator monitors the `Deposit` event from the Zkopru contract.

### **When does a MassDeposit become "committed"?**

It is important to push the deposits to the layer-2 as quickly as possible. Therefore, it freezes the latest MassDeposit when the coordinators propose every new block.

### **Can the coordinator include more than one MassDeposit?**

Yes, it is possible to include several MassDeposits at once in the range of maximum challengeable cost.

