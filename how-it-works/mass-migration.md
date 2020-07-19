# Mass migration

The basic idea of mass migration is pretty simple. While `deposit` transactions on the layer-1 contract create a `MassDeposit` object, "Migration" type outputs of transactions can create a `MassMigration` that constructs a `MassDeposit` for its destination network.

{% hint style="info" %}
A transaction can have UTXO, Migration, or Withdrawal type of outputs. To check more details, see ["Transaction" ](transaction.md)page.
{% endhint %}

In Zkopru, for the migration, there are source network and destination network. Once a mass migration on the source network is finalized\(code [here](https://github.com/zkopru-network/zkopru/blob/034ad7b41eca2a9fc0d344a5b5a8a4525e904c96/packages/contracts/contracts/controllers/Coordinatable.sol#L125)\), the [`migrateTo` function](https://github.com/zkopru-network/zkopru/blob/034ad7b41eca2a9fc0d344a5b5a8a4525e904c96/packages/contracts/contracts/controllers/Migratable.sol#L15) on the source network can be executed. The function moves the assets, including Ether, ERC20, and ERC721, while creating a MassDeposit object for the destination network.

Therefore, the destination network should implement [`acceptMigration` function](https://github.com/zkopru-network/zkopru/blob/034ad7b41eca2a9fc0d344a5b5a8a4525e904c96/packages/contracts/contracts/controllers/Migratable.sol#L52). Also, it is recommended to allow migrations only from trusted networks.

{% hint style="info" %}
The migration standard between rollups is going to be standardized through EIP.
{% endhint %}

