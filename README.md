---
description: >-
  IC4J Agent for The Internet Computer is a set of native Java libraries to
  connect remotely to the Internet Computer applications.
---

# Introduction

## Welcome to IC4J

Welcome to IC4J! Here you'll find all the documentation you need to get up and running with the IC4J API.&#x20;

To learn more about the Internet Computer platform, please visit Dfinity website.

_"The Internet Computer is created by the Internet Computer Protocol (“ICP”), which has formed the world’s first web-speed, web-serving public blockchain. The Internet Computer is self-governing and can grow its capacity as required. It combines special node machines run en masse by independent data centers all around the world. Like all blockchains, it is unstoppable, and the code it hosts is tamperproof."_

{% embed url="https://dfinity.org" %}

{% embed url="https://smartcontracts.org/docs/introduction/welcome.html" %}

The IC4J code is Java implementation of the Internet Computer Interface protocol.

{% embed url="https://sdk.dfinity.org/docs/interface-spec/index.html" %}

IC4J library is using Dfinity Rust agent as an inspiration, using similar package structures and naming conventions.

{% embed url="https://github.com/dfinity/agent-rs" %}

The Internet Computer is using two method types to execute smart contract code in the canister, UPDATE and QUERY. UPDATE method is mutable, allows user to change data on the chain. QUERY method is immutable. IC4J is using native ICP binary protocol so this way Java code can participate on chain updates and query canister data without using any gateway or bridge. This way communication between Java application and the IC canister is secure and tamperproof.&#x20;

![](.gitbook/assets/ICPSchema.png)

IC4J source code with samples can be found on Github.

{% embed url="https://github.com/ic4j" %}

## Want to jump right in?

Jump in to the quick start docs and get making your first request:

{% content-ref url="quick-start.md" %}
[quick-start.md](quick-start.md)
{% endcontent-ref %}

## Want to deep dive?

Dive a little deeper and start exploring our API reference to get an idea of everything that's possible with the API:

{% content-ref url="reference/api-reference/" %}
[api-reference](reference/api-reference/)
{% endcontent-ref %}

