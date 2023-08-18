# Importance of client diversity

Your Ethereum validator node is made up of various pieces of software clients working together to process and attest to transactions. Being a decentralised network, Ethereum is not dependent on any single physical server / node.&#x20;

However, if every node runs only on one single client set, the existence of crazy bugs or targeted attacks on the software layer can still disrupt this process.&#x20;

When this happens, we as validator node operators will suffer penalties. **These penalties increase exponentially when our validator nodes go offline together with a large portion of the network.**&#x20;

This is why there are at least 4 - 5 clients to choose from for execution layer and consensus layer clients today. With Geth owning more than 50% of the execution layer client market share today, the existence of a crazy bug causing validator nodes running Geth to double-sign will be slashed for their entire stake of 32 ETH.&#x20;

This is why we will be recommending minority clients for this guide.
