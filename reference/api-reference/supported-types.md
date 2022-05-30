# Supported Types

The IC4J **Candid Library** allows Java developers to serialize and deserialize Java native types to **IC Candid IDL types.**

_"Candid is an interface description language. Its primary purpose is to describe the public interface of a **service**, usually in the form of a program deployed as a **canister smart contract** that runs on the Internet Computer. One of the key benefits of Candid is that it is language-agnostic, and allows inter-operation between services and front-ends written in different programming languages, including Motoko, Rust, and JavaScript."_

{% embed url="https://smartcontracts.org/docs/candid-guide/candid-concepts.html" %}
What is Candid?
{% endembed %}

{% embed url="https://smartcontracts.org/docs/candid-guide/candid-types.html" %}
Candid Types
{% endembed %}

This table shows implicit mapping between Candid types and default Java type assignment.

| Candid    | Java       |
| --------- | ---------- |
| bool      | Boolean    |
| int       | BigInteger |
| int8      | Byte       |
| int16     | Short      |
| int32     | Integer    |
| int64     | Long       |
| nat       | BigInteger |
| nat8      | Byte       |
| nat16     | Short      |
| nat32     | Integer    |
| nat64     | Long       |
| float32   | Float      |
| float64   | Double     |
| text      | String     |
| opt       | Optional   |
| principal | Principal  |
| vec       | array      |
| record    | Map, Class |
| variant   | Map, Enum  |
| null      | Null       |
