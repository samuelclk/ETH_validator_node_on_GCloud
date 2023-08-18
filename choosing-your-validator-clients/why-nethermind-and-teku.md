# Why Nethermind & Teku?

## Key considerations

Even though we should run minority clients, it does not automatically mean that any client can run on the same hardware. A key reason for Geth's popularity, other than it's reliability, is the ability for it to run on minimal hardware

**Execution layer clients**

<table><thead><tr><th width="143">Client</th><th width="120">CPU speed</th><th width="81">RAM</th><th width="96">Storage</th><th width="131">SSD speeds</th><th width="74">Sync speed</th><th>Market share</th></tr></thead><tbody><tr><td><strong>Geth</strong></td><td>lowest</td><td>16 GB</td><td>2 TB</td><td>Good</td><td>2nd</td><td>57.88%</td></tr><tr><td><strong>Besu</strong></td><td>lower</td><td>32 GB</td><td>2 TB</td><td>Good</td><td>1st</td><td>9.93%</td></tr><tr><td><strong>Nethermind</strong></td><td>lower</td><td>32 GB</td><td>2 TB</td><td>Good</td><td>3rd</td><td>19.31%</td></tr><tr><td><strong>Erigon</strong></td><td>higher</td><td>32 GB</td><td>4 TB</td><td>Performance</td><td>4th</td><td>12.63%</td></tr></tbody></table>

_Source on market share -_ [_https://www.ethernodes.org/_](https://www.ethernodes.org/)

**Consensus layer clients**

Because the dependencies on hardware requirements is greater for the execution layer (EL) clients, the considerations for consensus layer (CL) clients will primarily be on market share.

<table><thead><tr><th width="158">Client</th><th>Market share</th></tr></thead><tbody><tr><td><strong>Prysm</strong></td><td>45.88%</td></tr><tr><td><strong>Lighthouse</strong></td><td>33.25%</td></tr><tr><td><strong>Teku</strong></td><td>14.97%</td></tr><tr><td><strong>Nimbus</strong></td><td>4.77%</td></tr><tr><td><strong>Lodestar</strong></td><td>1.13%</td></tr></tbody></table>

_Source on market share -_ [_https://clientdiversity.org/_](https://clientdiversity.org/)

Further, because all CL clients now have the checkpoint sync feature - enabling CL syncing in just a few minutes - we can rapidly switch between CL clients with minimal downtime when there are bugs in one.

## Recommended client set

#### **Execution layer client - Nethermind**

1. Because Erigon requires more powerful hardware, our choice will be between Nethermind and Besu
2. Given that Besu can sync faster than Nethermind, it makes sense to choose Nethermind as our initial EL client (before we deposit our ETH) and then spin up a Besu client if there is a bug on Nethermind
3. This will minimise downtime after our ETH is deposited

#### **Consensus layer client - Teku**

1. Given how we can rapidly switch between CL clients when needed, we will go with the most mature CL client of the remaining choices
2. Teku's documentation and command-line / API reference is also very well written, making it a suitable CL client for newcomers to use&#x20;
