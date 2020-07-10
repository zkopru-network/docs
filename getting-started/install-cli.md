# Install CLI

## Install CLI wallet

The zkopru wallet includes a full node\(light node is in progress\) and manages the user's own UTXOs and the proof data. To use zkopru, users need to install the zkopru wallet first. Currently, it only supports the CLI version, but it is expected to have a mobile version soon.

### Get `@zkopru/cli`

```text
$ npm install -g @zkopru/cli
```

Please install \`@zkopru/cli\` globally using npm. This command will install `zkopru-wallet` and `zkopru-coordinator`. We will use `zkopru-wallet` binary for this tutorial.

### Run `zkopru-wallet`

```text
$ zkopru-wallet
```

`zkopru-wallet` will connect the client to the layer-1 using WebSocket. The default WebSocket URL is [ws://testnet.zkopru.network:5555](ws://coordinator.zkopru.network:5555). However, it can be configured with another URL using the CLI option or configuration file.

Also, the default value of the coordinator's URL is [https://coordinator.zkopru.network](https://coordinator.zkopru.network). Later, it also can be configured with different coordinators' URLs.

```text
$ zkopru-wallet --ws ws://{CUSTOM_WEBSOCKET} --coordinator https://{COORDINATOR_URL}
```

This command will download the zk SNARK keys from the cloud storage.

```text
Downloading snark keys | [=--------------------] | 8% | 28047/344592 KiB | ETA: 25s
```

Finally, it automatically checks that the SNARK keys are correctly paired with the registered verifying keys on the layer-1 contract. Also the keys can be directly downloded [here](https://d2xnpw7ihgc4iv.cloudfront.net/keys.tgz).

### Configuration

```text
? You should configure database › - Use arrow-keys. Return to submit.
    Postgres(recommended)
❯   Sqlite
```

After downloading the keys, configure the database. Please select Sqlite. Then type the path where the client will save the data\(just press 'enter' key if you want to use the default path\).

```text
✔ You should configure database › Sqlite
? Provide sqlite db here › zkopru-wallet.db
```

Next, please configure the HD wallet using mnemonic words. You can create a new mnemonic word set or import existing keys.

```text
? You should import mnemonic keys or create a new one. › - Use arrow-keys. Return to submit.
❯   create
    import
```

Once you create or import keys, it will ask how many accounts you want to manage on the layer-2.

```text
List of tracking accounts

0: 0x531023e7a198c8c0839a7b713f4fdbe460792051
? This node will keep tracking on the utxos for these accounts. › - Use arrow-keys. Return to submit.
❯   Create a new account
    Proceed to the next step
```

If you select "create a new account," it will make a new derived Ethereum account using the BIP-39 algorithm. And then, save your configuration.

```text
✔ This node will keep tracking on the utxos for these accounts. › Proceed to the next step
✔ Do you want to save this configuration? … yes
✔ Type your password … **********
✔ Save to … wallet.json
```

If you save the configuration, you can run the wallet using the config file with `--config` option.

```text
$ zkopru-wallet --config wallet.json
```

### 

