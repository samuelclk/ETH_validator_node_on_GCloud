# Set up and configure consensus layer client (Teku)

### Choose your setup preference

_<mark style="background-color:yellow;">Navigate to the respective sub-sections depending on whether or not you want to use a external signer (Web3signer) in your setup.</mark>_

[Web3Signer](https://docs.web3signer.consensys.net/en/latest/) is a tool developed by Consensys to enable _remote signing_. This allows the Validator Client (VC) to outsource the signing of transactions/blocks to a separate (remote) server that only 'talks' to the VC and nothing else. This means the validator signing keystores can be isolated from the validator node itself, which can make the setup much more secure.

### Considerations for using Web3signer

1. Using an external signer introduces additional complexity in your setup, which translates to additional risks of messing up and attack vectors if it is not configured properly
2. Most non-professional users (e.g. home-stakers) can enjoy a similar heightened level of security by running the VC on a separate device/server than the beacon node pair
