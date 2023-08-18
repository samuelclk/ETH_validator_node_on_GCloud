# Rewards and penalties

## Rewards

In exchange for processing transactions on the Ethereum network and providing economic security, validators receive rewards in the form of issuance, transaction fees (tips), and MEV bribes. Issuance is received from performing attestations and tips + MEV bribes are received from proposing blocks.&#x20;

The breakdown of these fees on average are as follows:

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>As of 19/08/2023 on <a href="https://ultrasound.money/">ultrasound money</a></p></figcaption></figure>

With a 32 ETH stake, the average yield will be 1.92 ETH per annum. However, because the APR from tips and MEV bribes are random and tend to be large when they occur (i.e. a heavy right skew), the median total APR, which is what most validators will be getting is closer to 4.5% - 5.0%.&#x20;

These rewards are received every \~6.4 minutes (every epoch) and the accumulated balance is automatically withdrawn to your designated wallet every 4 - 5 days.

## Penalties

### Uncorrelated downtime

On the other hand, you will be penalised if your validator goes offline or is otherwise inactive. Under normal circumstances, your validator should not go offline together with other validators - i.e. "uncorrelated downtime" - and if so, your inactivity penalties per epoch will be roughly the same as your rewards rate per epoch. Unless of course, you miss your turn to propose a block while you were inactive.

This means that you typically don't have to panic when you start seeing missed attestations and should rather take your time to make sure you don't make a mistake when you troubleshoot your validator node.

### Correlated downtime

However, if your validator node goes down together with a large portion of the network, you will be penalised more heavily.&#x20;

In an extreme scenario, where more than 1/3 of the network goes offline, causing the chain to be in danger of splitting, the ["inactivity leak"](https://eth2book.info/capella/part2/incentives/inactivity/) will be triggered.

The inactivity leak is a state of emergency to compel the inactive 1/3 to resume their duties or otherwise removing them (and their ETH) from the network until the network has >2/3 of active validators once again.&#x20;

There are two main mechanisms that will occur during an inactivity leak:

1. **Normal attestation rewards will stop for all validators.** Block proposers will still receive their normal rewards from all 3 sources
2. **Quadratic leak** - where inactive validators will suffer increasing penalties that grows quadratically over time until the network has >2/3 of active validators once again
