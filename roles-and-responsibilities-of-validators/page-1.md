# Roles of a node operator

## What do validator nodes actually do?

In short, validator nodes on the Ethereum network _**process transactions and secure the network.**_ This is done via the [proof-of-stake ](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/)consensus mechanism where each validator puts 32 ETH at stake to vouch for the validity of new blocks _- with a bundle of transactions in them -_ that either others or themselves have created.

In the former, they would be the **Attesters** and, **Proposers** in the latter.

In exchange for doing the work above, validators receive rewards from both users and the Ethereum protocol directly. However, if validators are **caught acting dishonestly** by other nodes in the network, **their stake is slashed** - forcibly burning their 32 ETH based on the severity of their actions. This mechanism is further explained in the [Rewards and penalties sub-section](../understanding-eth-validators/rewards-and-penalties.md).

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Abstracted value chain of the ETH validator network</p></figcaption></figure>

To understand this process in more detail, let's walk through the value chain of the ETH validator network.

1. When users interact with Dapps built on top of the Ethereum network or directly sends Ethereum assets to one another, their transaction will first be sent into a holding area called the Mempool
   * MEV searchers are another group of players that observe the Mempool for arbitrage opportunities. When they find such opportunities, they insert their own transaction and submit the arbitrage transactions set to the block builders
   * Block builders then bundle these transactions into a block and pushes it to the upcoming block proposer via a relay (Mev-boost)&#x20;
2. The validator network picks up these transactions from the Mempool and randomly chooses a validator to serve as the proposer to propose a new block once every \~12 seconds (a slot)
3. This block proposer has \~4 seconds to receive, execute, and send the block back out
4. A committee of validators (the Beacon Committee) is chosen at random to determine the validity of this new block within the remaining \~8 seconds
5. Once the new block is proposed and sufficiently attested to, it is added to the blockchain
6. Every 32 slots (an epoch), the batch is finalised and the transactions in said batch can no longer be reversed without burning a large portion of staked ETH across the whole network
