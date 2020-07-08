# Account

Zkopru account manages various key sets. 

First of all, it has an Ethereum account from a randomly generated private key. This is used for interactions on the layer 1.

Secondly, it creates a Babyjubjub private key and public key set from the Ethereum account's private key. This set is used for the EdDSA on the layer 2.

Lastly, it has a zk public key which is a hash value of a Babyjubjub public key and a nullifier seed. Transaction builders can create UTXOs using this zk public key. Here, the nullifier seed is used to create a nullifier of a UTXO using its leaf index.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Key</th>
      <th style="text-align:left">Size</th>
      <th style="text-align:left">How to get</th>
      <th style="text-align:left">Where to use</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Master seed</td>
      <td style="text-align:left">32 byte</td>
      <td style="text-align:left">Randomly generated</td>
      <td style="text-align:left">Recover keys</td>
    </tr>
    <tr>
      <td style="text-align:left">Ethereum private key</td>
      <td style="text-align:left">32 byte</td>
      <td style="text-align:left">Derived from master seed with BIP39</td>
      <td style="text-align:left">Layer1 ECDSA</td>
    </tr>
    <tr>
      <td style="text-align:left">Ethereum public address</td>
      <td style="text-align:left">20 byte</td>
      <td style="text-align:left">Derived from private key</td>
      <td style="text-align:left">Layer 1 interactions</td>
    </tr>
    <tr>
      <td style="text-align:left">Jubjub private key</td>
      <td style="text-align:left">254 bit</td>
      <td style="text-align:left">Derived from Eth private key</td>
      <td style="text-align:left">Create EdDSA to spend UTXO</td>
    </tr>
    <tr>
      <td style="text-align:left">Jubjub public key</td>
      <td style="text-align:left">(254 bit, 254 bit)</td>
      <td style="text-align:left">Derived from jubjub private key</td>
      <td style="text-align:left">Proving ownership of a UTXO</td>
    </tr>
    <tr>
      <td style="text-align:left">Nullifier seed</td>
      <td style="text-align:left">254 bit</td>
      <td style="text-align:left">It equals to the Jubjub private key of this account.</td>
      <td style="text-align:left">
        <p>Creates a nullifier.</p>
        <p><code>nullifier = hash(</code>
        </p>
        <p><code>    nullifier_seed,</code>
        </p>
        <p><code>    utxo_index,</code>
        </p>
        <p><code>)</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Zk public key</td>
      <td style="text-align:left">254 bit</td>
      <td style="text-align:left">
        <p><code>poseidon(<br />    jubjub_pubkey.x,<br />    jubjub_pubkey.y,</code>
        </p>
        <p><code>    nullifier_seed,</code>
        </p>
        <p><code>)</code>
        </p>
      </td>
      <td style="text-align:left">Used to share public key without revealing its nullifier seed</td>
    </tr>
  </tbody>
</table>



