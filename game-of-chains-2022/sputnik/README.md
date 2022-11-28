# Sputnik Chain Information

Contents

* [Status](#status)
* [Chain Data](#chain-data)

## Status

* Timeline
  * 2022-11-11: The `goc-coordinator` validator will start signing on Sputnik at `2022-11-11T14:00:00.000000Z`.
  * 2022-11-10: Spawn time: `2022-11-10T14:00:00.000000Z`
  * 2022-11-09: Proposal 1 voting period ends
  * 2022-11-07: Proposal 1 goes into voting period
  * 2022-11-07: Chain initialized

Sputnik will launch as a consumer chain through a governance proposal in the `provider` chain. Read the [Consumer Chain Start Process](/docs/Consumer-Chain-Start-Process.md) page for more details about the workflow.

The following items will be included in the proposal:
* Genesis file hash
  * The SHA256 is used to verify against the genesis file that the proposer has made available for review.
  * This "fresh" genesis file cannot be used to run the chain: it must be updated with the CCV states after the spawn time is reached.
* Binary hash
* Spawn time
  * Even if the proposal passes, the CCV states will not be available from the provider chain until after the spawn time is reached.

## Chain Data

### Binary

The binary published in this repo is the `interchain-security-cd` binary built using the `interchain-security` repo tag [v0.2.0](https://github.com/cosmos/interchain-security/releases/tag/v0.2.0). You can generate the binary following the [build instructions](https://github.com/cosmos/interchain-security#instructions).

  * [Linux amd64 build](interchain-security-cd)
  * SHA256: `982f34f8edae365b6da5f8cb32fbe1f61f0317348663a21b5ebd6b21996760b9`

### Genesis file

**The genesis file for Interchain Security consumer chains must include the CCV (Cross Chain Validation) state generated by the provider chain _after_ the spawn time is reached.**

Final genesis file **with CCV state**: **[sputnik-genesis.json](sputnik-genesis.json)**
- SHA256: `030a9c8b752127e98ea8154389fab3f8d7d8acdfd768cd18c5b194fb4bebfd64`
- Validators must replace their `config/genesis.json` file with this one before running the binary.

The genesis file with was generated using the following settings:

* Chain ID: `sputnik`
* Denom: `unik`
* Signed blocks window: `"8640"`
* Two additional genesis accounts were added to provide funds for a faucet and a relayer that will be run by the testnet coordinators.
* Genesis file **without CCV state**: [`sputnik-fresh-genesis.json`](sputnik-fresh-genesis.json), SHA256: `ec5f03d8d3be2fbd3e8bd87c113379b671c300eeb8295863f785237e020040a9`
  * **This is provided only for verification, this is not the genesis file validators should be running their nodes with.**

## Endpoints

* **p2p seeds : `3aed29ec1ca96ea52299748c50bf7d908511068f@tenderseed.ccvalidators.com:29019`**
* **p2p persistent peers : `4b5cee15e6a9c4b96b8c1c4f396a18b0461edc17@46.101.106.107:26656,835173badfc41ecbd867a0395c6a452bda2bb90f@134.209.255.244:26656`**
* These peers represent the `goc-coordinator` and `goc-backup` validators (run by the testnet coordinators). 
* The `goc-backup` validator node will be running on Sputnik shortly after the genesis file that includes the CCV state (Cross Chain Validation state) has been published.
* The `goc-coordinator` validator node has an overwhelming majority of the voting power, and we aim to start it two hours after the spawn time is reached. 67% of the voting power needs to come online for consumer chains to start. Once the `goc-coordinator` is live, the chain will progress.
* Please keep in mind that any validator that does not come online after 67% of the voting power is up and running, is likely to be slashed for downtime, potentially resulting in being jailed (the `signed_blocks_window` parameter is set to `8640`).


## Join via Bash Script

On the node machine:
- Copy the `node_key.json` and `priv_validator_key.json` files for your validator.
  - **These should be the same ones as the ones from your provider node**.
- Run one of the following scripts:
  - Sputnik service: [sputnik-init.sh](sputnik-init.sh)
  - Cosmovisor service: [sputnik-init-cv.sh](sputnik-init-cv.sh)
- Wait until the spawn time is reached and the genesis file with the CCV states is available.
- Overwrite the genesis file with the one that includes the CCV states.
  - The default location is `$HOME/.sputnik/config/genesis.json`.
- Enable and start the service:
  - Sputnik
    ```
    sudo systemctl enable sputnik
    sudo systemctl start sputnik
    ```
  - Cosmovisor
    ```
    sudo systemctl enable cv-sputnik
    sudo systemctl start cv-sputnik
    ```
- To follow the log, use:
  - Sputnik: `journalctl -fu sputnik`
  - Cosmovisor: `journalctl -fu cv-sputnik`
- If the log does not show up right away, run `systemctl restart systemd-journald`.