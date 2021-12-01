# RPC API

Zkopru nodes offer an RPC API for accessing information about the L2 network and the underlying L1 network. The RPC format is similar to that of Ethereum. One major difference is not all numbers are hexadecimal. Any numbers without a 0x prefix should be assumed to be decimal.

Zkopru nodes will proxy web3 requests to the underlying Ethereum node. For example, `eth_blockNumber` will return a valid response. Zkopru node URLs can be used as web3 providers.

A testnet API can be accessed at `https://node.zkopru.network/`

## Transports

The API currently supports HTTP(s) transports only. Requests should be sent with the `POST` method and `Content-Type: application/json`.

## Payload Structure

Each request has a payload structured like the following:

```js
{
  "id": 20, // a unique request identifier, may be a number or string
  "jsonrpc": "2.0", // the jsonrpc version, 2.0 is the only currently accepted value
  "method": "l2_getBlockByNumber", // the rpc method to call
  "params": [1] // an array of parameters to pass to the method
}
```

Each response will include a `result` and an optional `error`.

```js
{
  "id": 20,
  "jsonrpc": "2.0",
  "result": { ... },
}
```

## Methods

### `l1_address`

Returns the address of the Zkopru contract on the L1 network.

- Params: none
- Result: 20 byte address

### `l1_getVKs`

Returns the verifying keys for the current Zkopru network.

- Params: none
- Result:

### `l2_syncing`

Returns a boolean indicating whether the current node is synchronizing.

- Params: none
- Result: Boolean

### `l2_blockCount`

Returns the number of blocks that have been submitted to the current Zkopru network. This includes uncles. This **is not** the same as the block number. The block count will always be greater than or equal to the block number.

- Params: none
- Result: Integer

### `l2_blockNumber`

Returns the number of blocks in the canonical Zkopru chain. This is referred to as the **canonical number** in object properties.

If you're not sure whether to use the block count or block number, you probably want block number.

- Params: none
- Result: Integer

### `l2_getBlockByNumber`

Returns a block by canonical number. Optionally include uncle blocks for the same number. If uncles are not included a property `uncleCount` will be present on the response.

- Params: `index (Integer)`, `includeUncles (Boolean)`
- Result: Object

Example request:

```json
{
  "id": "b14d0d78-722d-4c50-a1a8-a3051bdff54e",
  "jsonrpc": "2.0",
  "method": "l2_getBlockByIndex",
  "params": [
    6
  ]
}
```

Example response:

```json
{
  "id": "b14d0d78-722d-4c50-a1a8-a3051bdff54e",
  "jsonrpc": "2.0",
  "result": {
    "hash": "0xc7124d497b2d168491cf4edccacb57944825998df0ca240b5d5b1e9de14b818b",
    "proposalNum": "0x2",
    "canonicalNum": "0x2",
    "proposedAt": "0x3f9d3b",
    "proposalTx": "0x391716f4062aae7de3e33b515d069868eef6b32a315224aa859811883567444e",
    "fetched": "0xc7124d497b2d168491cf4edccacb57944825998df0ca240b5d5b1e9de14b818b",
    "finalized": true,
    "isUncle": null,
    "header": {
      "proposer": "0x38F5d408E939DDD7284d7D4F0efcB646AD3AfeBe",
      "parentBlock": "0x23b65964be40442b9fd8554113d13833d2b74ecabffd12e3fe99e8abefa56c66",
      "fee": "1000000000000000",
      "utxoRoot": "20862818552041851883241619460083587253359785886706840054650018676916893910761",
      "utxoIndex": "64",
      "nullifierRoot": "0xbdaf0a25a0628973d4df7e4c053cda1318b39801d3f0fba827ebe792ec12cbfa",
      "withdrawalRoot": "112698963998666297625327584077802398874583062656002324209327761713137447869541",
      "withdrawalIndex": "0",
      "txRoot": "0x0000000000000000000000000000000000000000000000000000000000000000",
      "depositRoot": "0x4da7b828a0df7497b8213ff38f0c86430d31176be4577971c2b41d24f6175a61",
      "migrationRoot": "0x0000000000000000000000000000000000000000000000000000000000000000"
    },
    "body": {
      "txs": [],
      "massDeposits": [
        {
          "merged": "0x8039b6e6adc4887b22671537038b0853eb488985da6e4f29a97daf696c985741",
          "fee": "1000000000000000"
        }
      ],
      "massMigrations": []
    }
  }
}
```

### `l2_getBlockByIndex`

Returns a block by block index. This is **not** the canonical number but the block index in the network, including uncles.

- Params: `index (Integer)`
- Result: Object

### `l2_getBlockByHash`

Returns a Zkopru block by hash.

- Params: `hash (String)`
- Result: Object

### `l2_getTransactionByHash`

Returns transaction data by hash.

- Params: `hash (String)`
- Result: Object

Example request:

```json
{
  "id": "21bd6e26-f3d4-4070-bd04-2dd9bcaef35f",
  "jsonrpc": "2.0",
  "method": "l2_getTransactionByHash",
  "params": [
    "0xe9a2c21a92ed49a590106b37739d0708d22e7ebbddc82b184673e1ef1e22b58e"
  ]
}
```

Example response:

```json
{
  "id": "21bd6e26-f3d4-4070-bd04-2dd9bcaef35f",
  "jsonrpc": "2.0",
  "result": {
    "blockHash": "0x5a0e5b4f2be8303830f6201aca68e58504a1fc8a914896c3bc2adc52d4d0bcab",
    "hash": "0xe9a2c21a92ed49a590106b37739d0708d22e7ebbddc82b184673e1ef1e22b58e",
    "size": "0x1f6",
    "inflow": [
      {
        "root": "0x04bd3d85579ac9fc1207232f89d4bc00375d1fd7b8456be8c6955cd65c6c4382",
        "nullifier": "0x1e64c15fa8fca85dac41a458639559234e7c0ab4bb640fb5e17d659f19b42636"
      }
    ],
    "outflow": [
      {
        "note": "0x016d3f541ec8299c772c30cd50f8788747a6102c97ed85a3de96a65b4a2bf234",
        "outflowType": "0x00"
      },
      {
        "note": "0x12f6ee5286090fab08e06500e7842017bd4f4cb436aa8c457fd9016846f19a3e",
        "outflowType": "0x00"
      }
    ],
    "fee": "0x2165400ce38000",
    "proof": {
      "pi_a": [
        "0x25d00948974f3c999638a3b3e6a9d999db2ae2dd4c3e32ddd3837254b2eb3ac4",
        "0x14d36d567ef8388679840986b7fc206951f82aee4661a2c07be07d1c34e79b5a"
      ],
      "pi_b": [
        [
          "0x279dd8f2f3df9833293be7577a3cfeb4638f4ae7e14e079db6ace9c14c27f1b4",
          "0x2c057e4e22437ed9f6947e72a5dd74cdbd6d40f94a6628c3ac674e102eb587ed"
        ],
        [
          "0x1cd22b3975187a3fb07acb3f42a5b2480817e03f78a1dd218657cb005a89d381",
          "0x2e733a138696814984b3e259812e94c82101c798cf64e12c12b29532cd187d3a"
        ]
      ],
      "pi_c": [
        "0x2f9a3d2b1787e14a8b79d350328a4a05f9cd1708eacfbf70c45278394ccd28dd",
        "0x0f37fc45fac4aba7e1f525094c2c13b87597b3c8db7dbb6f4765c5739f6c8b06"
      ]
    },
    "memo": "o10oNamv+WnnvMRb/gO5GgAmBHOzOrnnhBwni/PnVBoUhhr07UA3HibL7sqQnbLzihnoLiq3w1r/bLlw1MViHdRY496ZbdBEYRR20MIN0TLY"
  }
}
```

### `l2_getRegisteredTokens`

Returns the tokens registered for use in the network.

- Params: none
- Result: Object

Example response:

```json
{
  "id": "9509f206-8247-4ed7-bff4-45cfbdba5fea",
  "jsonrpc": "2.0",
  "result": {
    "erc20s": [
      "0x9cAc5281Ef219F402F8c350925e1b065A03cf46E"
    ],
    "erc721s": [
      "0xccb6FD36Ad72f132fDfe864Db81d0c0166ED227D"
    ]
  }
}
```
