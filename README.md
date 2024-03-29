# Stakesaurus guide to ETH staking on Google Cloud

## Welcome to the guide

If you are coming from Github, check out the Gitbook version here for a better reading experience - [https://stakesaurus.gitbook.io/gcloud-eth-validator-node-setup-guide/](https://stakesaurus.gitbook.io/gcloud-eth-validator-node-setup-guide/)

### Need 1-to-1 guided support? Drop by and say hi to us!

{% embed url="https://www.stakesaurus.com/" %}

or email me at **`sam@stakesaurus.com`**

## Credits

This guide references and builds upon several other guides that came before mine and I could not have been able to run my own validator nodes without them. Please check out some of these OG contributors here - [Ethstaker](https://docs.ethstaker.cc/ethstaker-knowledge-base/), [CoinCashew](https://www.coincashew.com/coins/overview-eth/guide-or-how-to-setup-a-validator-on-eth2-mainnet), [Somer Esat](https://github.com/SomerEsat/ethereum-staking-guides), [Remy Roy](https://github.com/eth-educators/ethstaker-guides/blob/main/prepare-for-the-merge.md), [Rocketpool ](https://docs.rocketpool.net/guides/)

## Why write another ETH staking guide?

While there are already existing guides out there on how to set up an ETH validator node, I find that they sometimes assume some basic level of working technical knowledge.&#x20;

This can result in seemingly obvious but important steps being skipped, which prevents completely non-technical but aspiring node operators from pushing through.

Hence, I put this guide together catering to the laymen. With this, I hope to bring you guys from zero-to-one on your journey as an ETH validator node operator!

**Some key features of this guide:**

1. Highly copy-paste-able shell bash commands + step-by-step instructions for all non-command procedures
2. Catered towards using Google Cloud's services for the staking infrastructure / hardware
3. Optimised to preventing slashing and inactivity leaks (instead of cost)
4. Covers the conceptual understanding of how validators work under the hood and how slashing happens
5. How to decide which validator clients to use as a newcomer
6. Nethermind (EL) + Teku (CL) client set

Coming up next, I will also be building more guides covering the following:

1. <mark style="background-color:orange;">different client sets</mark>
2. <mark style="background-color:blue;">cost-efficient home setups</mark>&#x20;
3. <mark style="background-color:green;">enterprise-level setups</mark>&#x20;
4. <mark style="background-color:purple;">specific use cases</mark> - e.g. migrating clients, setting backup nodes, external signer & HSM, MEV Boost, Telegram/Discord monitoring bots

**Drop your email below so you don't miss it!**

{% @mailchimp/mailchimpSubscribe %}

## Want to jump right in?

Feeling like an eager beaver? Jump in to the quick start docs and begin your journey as an ETH validator!

{% content-ref url="quick-start.md" %}
[quick-start.md](quick-start.md)
{% endcontent-ref %}



