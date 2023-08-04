# run-your-own-agent

This repository will help you to set up and run your own agent for the Beamer
bridge network. Just follow the instructions below to get started.

## Requirements

> We recommend setting up one instance of an agent for each rollup pair that you
> are planning to provide liquidity for.

### Hardware

Minimum recommended for a production setup:

* 2 Cores
* 4 GiB RAM

### Software

* Docker 18.06.0+, or an equivalent runtime with compose support

### RPC endpoints

You will need to configure RPC endpoints for both rollups you want to support,
as well as the Ethereum L1. You can either [host a node
yourself](https://ethereum.org/en/developers/docs/nodes-and-clients/run-a-node/)
or use a service like [Infura](https://infura.io/). Regardless, each rollup RPC
endpoint needs to be able to serve at least 200,000 agent's requests per day,
and potentially more, depending on traffic. Please make sure that the chosen
service is able to handle this.

### Account

We recommend creating a new account which will only be used for this specific
instance of the agent. The amount of funding determines the amount of transfers an agent can fulfill per day.

This account needs 

* ETH
* Liquidity in tokens you wish to support

The amount of ETH needed can be derived from the claimStake.

Example (and our recommendation):

* ETH on L1
  * claimStake = 0.01 ETH (required per transfer)
  * 200 transfers per day * claimStake = 2 ETH
  * We highly recommend a L1 resolution buffer of an additional 0.5 ETH
  * **recommended amount: 2.5 ETH**
* ETH on L2s
  * claimStake = 0.0015 ETH (required per transfer)
  * 200 transfers per day * claimStake = 0.3 ETH
  * **recommended amount: 0.3 ETH**

Additionally, make sure that your account is whitelisted for the mainnet deployment.
If it is not, you can apply for whitelisting at hello@beamerbridge.com.

## Installation

1. Provision a server that meets the hardware and software requirements listed
   above.

2. Clone the [current version of this
   repository](https://github.com/beamer-bridge/run-your-own-agent) to
   a suitable location on the server.

   ```shell
   git clone https://github.com/beamer-bridge/run-your-own-agent.git
   ```

3. Download deployment information into the `data` directory:

    ```shell
    cd run-your-own-agent
    curl -sSfL https://registry.npmjs.org/@beamer-bridge/deployments/-/deployments-2.1.0.tgz |
         tar xz -C data --strip-components=1 package/dist
    ```

   The above command will download Beamer contracts' deployment information which the
   agent needs in order to run.

4. Copy your JSON keystore file to `data/account`.


### Mainnet configuration

1. Edit `data/agent-mainnet.conf` and make sure that the following keys have correct values:

    - `[account.path]` - the path to your JSON keystore file
    - `[account.password]` - the password to unlock your keystore file
    - `[base-chain.rpc-url]` - the RPC endpoint to use for L1 resolution on mainnet Ethereum (e.g. `https://mainnet.infura.io/v3/ID`)
    - `[chains.ethereum.rpc-url]` - the RPC endpoint to use for filling requests on mainnet Ethereum (e.g. `https://mainnet.infura.io/v3/ID`)
    - `[chains.arbitrum.rpc-url]` - the RPC endpoint to use for mainnet Arbitrum (e.g. `https://arb1.arbitrum.io/rpc`)
    - `[chains.optimism.rpc-url]` - the RPC endpoint to use for mainnet Optimism (e.g. `https://mainnet.optimism.io`)
    - `[chains.polygon-zkevm.rpc-url]` - the RPC endpoint to use for mainnet Polygon zkEVM (e.g. `https://zkevm-rpc.com`)

   When configuring RPC endpoints, please consider rate limits that may be in
   place since those may affect agent operation.

   The default `data/agent-mainnet.conf` file configures the agent to bridge USDC and DAI
   between Arbitrum and Optimism. Please delete the lines for the other token in the 
   configuration file if you only want to bridge one of the tokens. For more details on 
   various configuration options, please refer to 
   [agent documentation](https://docs.beamerbridge.com/configuration.html).

### Testnet configuration

1. Similarly to the mainnet configuration, edit `data/agent-goerli.conf` and make
   sure that the following keys have correct values:

    - `[account.path]` - the path to your JSON keystore file
    - `[account.password]` - the password to unlock your keystore file
    - `[base-chain.rpc-url]` - the RPC endpoint to use for L1 resolution on Goerli Ethereum
    - `[chains.goerli.rpc-url]` - the RPC endpoint to use for filling requests on Goerli Ethereum
    - `[chains.arbitrum.rpc-url]` - the RPC endpoint to use for Goerli Arbitrum
    - `[chains.optimism.rpc-url]` - the RPC endpoint to use for Goerli Optimism
    - `[chains.polygon-zkevm.rpc-url]` - the RPC endpoint to use for Goerli Polygon zkEVM

   Other configuration settings are alredy prepared for testnet usage,
   including the test token configuration.

   The default `data/agent-goerli.conf` file configures the agent to bridge a
   test token between Optimism and Arbitrum.

   Note that you will also need to request whitelisting at hello@beamerbridge.com.

## Running agents

1. For mainnet, run:

   ```
   docker compose -f docker-compose-mainnet.yml up -d mainnet-agent
   ```

   For testnet, run:

   ```
   docker compose -f docker-compose-goerli.yml up -d goerli-agent
   ```

   The services are configured to automatically restart in case of a crash or reboot.

1. Check the logs to make sure everything went well and there are no errors.
   For mainnet:

   ```
   docker compose -f docker-compose-mainnet.yml logs -f
   ```

   For testnet:

   ```
   docker compose -f docker-compose-goerli.yml logs -f
   ```

You are now running an agent for the Beamer bridge. Thank you and please
[contact us](mailto:contact@beamerbridge.com) in case of any problems.

For more information on running an agent and updating to new agent versions,
please refer to [documentation](https://docs.beamerbridge.com/running.html).

## Beamer health-check

The beamer executable includes a health-check command that can be used to
analyze the beamer contracts and also reports agent liquidity. The health-check command
fetches all the events emitted by the contracts and analyzes them to determine
whether there are missed fills, unclaimed requests or challenges. The script will
send a notification to telegram or rocketchat depending on the configuration.

To find out how to configure the health-check, please refer to the health-check 
[documentation](https://docs.beamerbridge.com/configuration.html#notification-system).
