# Duality Chain Information
Welcome to Duality's Game of Chains testnet!

Contents

* [Status](#status)
* [Chain Data](#chain-data)

## Status

* Timeline
  * 2022-11-25: Chain initialized
  * 2022-11-25: Proposal 1 goes into voting period
  * 2022-11-28: Proposal 1 voting period ends
  * 2022-11-28: Spawn time: `2022-11-28T14:00:00.000000Z`
  * 2022-11-29: The `goc-coordinator` validator will start signing on Duality at `2022-11-29T14:00:00.000000Z`.


Duality will launch as a consumer chain through a governance proposal in the `provider` chain. Read the [Consumer Chain Start Process](https://github.com/hyphacoop/ics-testnets/blob/main/docs/Consumer-Chain-Start-Process.md#consumer-chain-start-process) page for more details about the workflow.

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
  * SHA256: `[to fill in]`

### Genesis file

**The genesis file for Interchain Security consumer chains must include the CCV (Cross Chain Validation) state generated by the provider chain _after_ the spawn time is reached.**

Final genesis file **with CCV state**: **[genesis.json](genesis.json)**
- SHA256: `4bb82a71d22f71e1bf5ffebc28e28f1986243418846663a2c16c1a5a60757d70`
- Validators must replace their `config/genesis.json` file with this one before running the binary.

The genesis file with was generated using the following settings:

* Chain ID: `duality`
* Denom: `dual`
* Signed blocks window: `"8640"`
* Two additional genesis accounts were added to provide funds for a faucet and a relayer that will be run by the testnet coordinators.
* Genesis file **without CCV state**: [`duality-fresh-genesis.json`](duality-fresh-genesis.json), SHA256: `[to fill in]`
  * **This is provided only for verification, this is not the genesis file validators should be running their nodes with.**

## Endpoints

* **p2p seeds : `[to fill in]`**
* **p2p persistent peers : `[to fill in]`**
* These peers represent the `goc-coordinator` and `goc-backup` validators (run by the testnet coordinators). 
* The `goc-backup` validator node will be running on Duality shortly after the genesis file that includes the CCV state (Cross Chain Validation state) has been published.
* The `goc-coordinator` validator node has an overwhelming majority of the voting power, and we aim to start it two hours after the spawn time is reached. 67% of the voting power needs to come online for consumer chains to start. Once the `goc-coordinator` is live, the chain will progress.
* Please keep in mind that any validator that does not come online after 67% of the voting power is up and running, is likely to be slashed for downtime, potentially resulting in being jailed (the `signed_blocks_window` parameter is set to `8640`).


## Join via Bash Script

On the node machine:
- Copy the `node_key.json` and `priv_validator_key.json` files for your validator.
  - **These should be the same ones as the ones from your provider node**.
- Run one of the following scripts:
  - Duality service: [duality-init.sh](duality-init.sh)
  - Cosmovisor service: [duality-init-cv.sh](duality-init-cv.sh)
- Wait until the spawn time is reached and the genesis file with the CCV states is available.
- Overwrite the genesis file with the one that includes the CCV states.
  - The default location is `$HOME/.duality/config/genesis.json`.
- Enable and start the service:
  - Duality
    ```
    sudo systemctl enable duality
    sudo systemctl start duality
    ```
  - Cosmovisor
    ```
    sudo systemctl enable cv-duality
    sudo systemctl start cv-duality
    ```
- To follow the log, use:
  - Duality: `journalctl -fu duality`
  - Cosmovisor: `journalctl -fu cv-duality`
- If the log does not show up right away, run `systemctl restart systemd-journald`.

