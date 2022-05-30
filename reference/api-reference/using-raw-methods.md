# Using Raw Agent Methods

Another option to invoke QUERY and UPDATE canister methods from Java is to use raw [Agent](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/Agent.java) methods **queryRaw** and **updateRaw**.

```java
CompletableFuture<byte[]> response = agent
    .queryRaw(Principal.fromString(canisterId),
        Principal.fromString(effectiveCanisterId),
         "echoInt",
          payload,
          ingressExpiryDatetime);
```

```java
CompletableFuture<byte[]> response = agent
    .updateRaw(Principal.fromString(canisterId),
        Principal.fromString(effectiveCanisterId),
         "greetjav",
          payload,
          ingressExpiryDatetime);
```

To get the status of the Internet Computer, use **status** Agent method. It will return the [Status](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/Status.java) Java type as follows.

```java
Status status = agent.status().get();
```
