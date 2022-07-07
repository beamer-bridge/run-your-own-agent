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

### Account

We recomment creating a new account which will only be used for this specific
instance of the agent. This account needs:
* L1 Eth, FIXME: amounts
* Eth on both rollups, at least 9 ETH per rollup, though we recommend 36 ETH
* Tokens used to fulfill transfer requests: at least 50,000 DAI per rollup,
  recommended 200,000 DAI.


## Installation
1. Provision a server that meets the hardware and software requirements listed
   above.

1. Clone the [current version of this
   repository](https://github.com/beamer-bridge/run-your-own-agent) to
   a suitable location on the server.

   ```shell
   git clone https://github.com/beamer-bridge/run-your-own-agent.git
   ```

1. Copy `.env.template` to `.env` and modify the values to fit your setup.
   Please read [Configuring the `.env` file](#configuring-the-env-file) for
   detailed information.

   - We recommend that you provide your own monitoring. The setup of which is
     currently out of scope of this document.
   - Please read the disclaimer for the agent carefully and uncomment the
     variable `AGENT_ACCEPT_DISCLAIMER` if you agree. Note, that without
     agreement the agent won't start.

1. Check that your account is whitelisted for the mainnet deployment. If not,
   you can apply for whitelisting at hello@beamerbridge.com.

1. Run `docker compose up -d` to start all services.
   - The services are configured to automatically restart in case of a crash or
     reboot.

You are now running an agent for the Beamer bridge. Thank you and please
[contact us](mailto:contact@beamerbridge.com) in case of any problems.

## Configuring the `.env` file
After cloning the repository the `.env` file needs to be configured. A template
named `.env.template` is provided. Below you find a detailed list of the
parameters to be set and their explanations.

- `KEYSTORE_FILE`: The keystore file which has to be located in ./keystore .
- `PASSWORD`: Password to decrypt the keystore file
- `L1_RPC_URL`: Ethereum RPC URL for the L1 chain. This is used to communicate
  with the Ethereum blockchain. You can either [host a node
  yourself](https://ethereum.org/en/developers/docs/nodes-and-clients/run-a-node/)
  or use a service like [Infura](https://infura.io/). Regardless, the agent
  requires a minimum of 200,000 requests per day and potentially more depending
  on traffic. Please make sure that the chosen service is able to handle this.
- `L2A_RPC_URL`: Ethereum RPC URL for the first rollup. This is used to
  communicate with the Ethereum blockchain. You can either [host a node
  yourself](https://ethereum.org/en/developers/docs/nodes-and-clients/run-a-node/)
  or use a service like [Infura](https://infura.io/). Regardless, the agent
  requires a minimum of 200,000 requests per day and potentially more depending
  on traffic. Please make sure that the chosen service is able to handle this.
- `L2B_RPC_URL`: Ethereum RPC URL for the second rollup. This is used to
  communicate with the Ethereum blockchain. You can either [host a node
  yourself](https://ethereum.org/en/developers/docs/nodes-and-clients/run-a-node/)
  or use a service like [Infura](https://infura.io/). Regardless, the agent
  requires a minimum of 200,000 requests per day and potentially more depending
  on traffic. Please make sure that the chosen service is able to handle this.
- `LOG_LEVEL`: 'debug' or 'info' recommended
- `DEPLOYMENT_DIR`: The directory that contains deployment information about the
  contracts necessary for running the agent.
- `TOKEN_MATCH_FILE`: The file that describes which tokens are considered
  equivalent.


### Deployment information
The agent requires information about the set of smart contracts to use. This
information is not bundled with the agent. Please download it from the release
page of the agent.
FIXME: Add link, or how do we distribute this?

### Token match file
The token match file contains information on which token contracts on L1 and L2s
represent the same canonical token.

FIXME: Do we want to provide an example or let people figure this out
themselves?
