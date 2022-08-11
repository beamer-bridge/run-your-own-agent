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
* 50 GiB storage

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

We recomment creating a new account which will only be used for this specific
instance of the agent. This account needs:
* At least 0.5 L1 ETH
* Eth on both rollups, at least 9 ETH per rollup, though we recommend 36 ETH
* Tokens used to fulfill transfer requests: at least 50,000 USDC per rollup,
  recommended 200,000 USDC.

Additionally, make sure that your account is whitelisted for the mainnet deployment.
If it is not, you can apply for whitelisting at hello@beamerbridge.com.


## Installation
1. Provision a server that meets the hardware and software requirements listed
   above.

1. Clone the [current version of this
   repository](https://github.com/beamer-bridge/run-your-own-agent) to
   a suitable location on the server.

   ```shell
   git clone https://github.com/beamer-bridge/run-your-own-agent.git
   ```

1. Download deployment information into the `data` directory:

    ```shell
    cd run-your-own-agent
    curl -sSfL https://github.com/beamer-bridge/beamer/archive/refs/heads/main.tar.gz |
         tar xz -C data --strip-components=1 beamer-main/deployments
    ```

    The above command will download Beamer contracts' deployment information which the
    agent needs in order to run.

1. Copy your JSON keystore file to `data/account`.


### Mainnet configuration

1. Edit `data/agent-mainnet.conf` and make sure that the following keys have correct values:

   - `[account.path]` - the path to your JSON keystore file
   - `[account.password]` - the password to unlock your keystore file
   - `[chains.l1.rpc-url]` - the RPC endpoint to use for mainnet Ethereum (e.g. `https://mainnet.infura.io/v3/ID`)
   - `[chains.boba.rpc-url]` - the RPC endpoint to use for mainnet Boba (e.g. `https://replica.boba.network`)
   - `[chains.optimism.rpc-url]` - the RPC endpoint to use for mainnet Optimism (e.g. `https://mainnet.optimism.io`)

   When configuring RPC endpoints, please consider rate limits that may be in
   place since those may affect agent operation.

   The default `data/agent-mainnet.conf` file configures the agent to bridge USDC
   between Boba and Optimism. For more details on various configuration options, please
   refer to [agent documentation](https://docs.beamerbridge.com/configuration.html).


### Testnet configuration

1. Similarly to the mainnet configuration, edit `data/agent-rinkeby.conf` and make
   sure that the following keys have correct values:

   - `[account.path]` - the path to your JSON keystore file
   - `[account.password]` - the password to unlock your keystore file
   - `[chains.l1.rpc-url]` - the RPC endpoint to use for Rinkeby Ethereum

   Other configuration settings are alredy prepared for testnet usage,
   including the test token configuration.

   The default `data/agent-rinkeby.conf` file configures the agent to bridge a
   test token between Boba and Metis.

   Note that you will also need to request whitelisting at hello@beamerbridge.com.

## Running agents

1. For mainnet, run:

   ```
   docker compose -f docker-compose-mainnet.yml up -d
   ```

   For testnet, run:

   ```
   docker compose -f docker-compose-rinkeby.yml up -d
   ```

   The services are configured to automatically restart in case of a crash or reboot.

1. Check the logs to make sure everything went well and there are no errors.
   For mainnet:

   ```
   docker compose -f docker-compose-mainnet.yml logs -f
   ```

   For testnet:

   ```
   docker compose -f docker-compose-rinkeby.yml logs -f
   ```

You are now running an agent for the Beamer bridge. Thank you and please
[contact us](mailto:contact@beamerbridge.com) in case of any problems.

We recommend that you provide your own monitoring. Setting up agent monitoring
is out of scope of this document.
