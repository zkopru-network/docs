# Configure Coordinator

## Install coordinator CLI

The Zkopru coordinator will synchronize the Zkopru chain, check for fraud, and optionally submit transactions and bid on auction rounds. To start install the Zkopru CLI:

```
$ npm install -g @zkopru/cli
```

A coordinator can now be run using `zkopru-coordinator`.

## Configuring

The coordinator has a number of options that can be configured via cli flags or a configuration file.

```sh
zkopru-coordinator <command> [args]

Options:
  --version          Show version number                               [boolean]
  --address, -a [string] [default: "0x970e8f18ebfEa0B08810f33a5A40438b9530FBCF"]
  --bootstrap, -b                                      [boolean] [default: true]
  --websocket, --ws        [string] [default: "ws://goerli.zkopru.network:8546"]
  --sqlite                                                              [string]
  --postgres                                                            [string]
  --maxBytes                                          [number] [default: 131072]
  --priceMultiplier                                       [number] [default: 48]
  --port                                                [number] [default: 8888]
  --config           You can skip interactive booting up process with JSON
                     configuration file                                 [string]
  --generateConfig   Generate a sample config file                      [string]
  --daemon, -d       Start as a daemon                                 [boolean]
  --password                                                            [string]
  --passwordFile     Path to a plaintext file to be used as the keystore
                     password                                           [string]
  --keystoreFile     Path to an Ethereum keystore file                  [string]
  --maxBid           Maximum bid allowed in the burn auction (Gwei)
                                                       [number] [default: 20000]
  --publicUrls       Comma separated list of host:port combinations this node is
                     accessible at                                      [string]
  --help             Show help                                         [boolean]
```
## Configuration File

All command line flags may be supplied in a json file. This file should be passed in using `--config`. **Any cli flags will override config file settings.**

## Generate Config

Zkopru includes a config generator that can be run using `zkopru-coordinator --generateConfig`. This will start an interactive process allowing you to select values for a configuration file. This file is a good starting point for further customization.

<img width="500px" height="auto" src="https://user-images.githubusercontent.com/631020/105255745-d0473580-5b49-11eb-8e94-8a31eb04b7b5.png" />
