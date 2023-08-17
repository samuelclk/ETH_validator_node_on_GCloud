# Hardware & system requirements

## Requirements

Despite being catered towards home-staking, running an ETH validator node reliably still requires some thought on the choice of hardware. To run this on Google Cloud, you will need:

1. CPU: E2 with 8 vCPU and 4 cores&#x20;
2. RAM: 32GB&#x20;
3. Storage: 2TB SSD persistent disk
4. Operating system: Ubuntu 22.04 LTS

## Architecture

1. Our setup will include a main validator node, a backup/failover beacon node, and an external signer that only communicates with the validator node.
   * Main validator node: Custom VM
   * Backup beacon node: Google Blockchain Engine (GBE)
   * External signer: Custom VM
2. We are using a custom VM for our main validator node because the GBE only provides the Geth/Lighthouse client pair for full nodes and we want to use minority clients for our main node
3. All 3 VMs will sit within the same virtual private cloud (VPC) so that they can communicate securely with one another.
4. We will implement Google Cloud Armor (GCA) on the main validator node to mitigate DDOS attacks. GCA on the backup beacon node is provided by default.

_**\*insert diagram**_

## Cost considerations

### Main validator node

Because the estimated monthly running costs on Google Cloud from just the validator node is   **USD 615.39** for the Singapore region**,** the economics will not make sense if you are planning on just running a single validator.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2023-08-16 at 5.55.10 PM.png" alt=""><figcaption></figcaption></figure>

A single validator generating 6.0% yield at ETH prices of USD1800 will give you USD3456 per year, or USD288 per month. This means you will need to run at least 3 validators by depositing 96 ETH for this method to make sense economically.

### Backup node

Further, if you want to set up a backup beacon node with the [google blockchain node engine](https://cloud.google.com/blockchain-node-engine/pricing), it will cost an additional USD 0.69 per hour - or USD 496.80 per month.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 2.51.05 PM.png" alt=""><figcaption></figcaption></figure>

### External signer

For the external signer, this will cost another USD 33.69 per month for the Singapore region.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 3.35.26 PM.png" alt=""><figcaption></figcaption></figure>

### Total costs

All in, the full setup will cost USD 1,139.85 per month. This means you have to run at least 4 validators (128 ETH) to breakeven.
