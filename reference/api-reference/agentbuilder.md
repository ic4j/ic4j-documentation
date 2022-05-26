# AgentBuilder

To create an [Agent](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/Agent.java) Java object the [AgentBuilder](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/AgentBuilder.java) build method is used.&#x20;

1\) Use transport method passing [ReplicaTransport](replicatransport.md) parameter.

```java
new AgentBuilder().transport(replicaTransport);
```

2\) Use identity method passing [Identity](identity.md) parameter.

```java
new AgentBuilder().identity(identity)
```

3\) IngresExpiry provides a default ingress expiry. This is the delta that will be applied at the time an update or query is made. Use Java [Duration](https://docs.oracle.com/javase/8/docs/api/java/time/Duration.html) type parameter.

```java
new AgentBuilder().ingresExpiry(Duration.ofSeconds(300));
```

4\) Creation of an Agent object.

```java
Agent agent = new AgentBuilder().transport(replicaTransport)
.identity(identity)
.ingresExpiry(Duration.ofSeconds(300))
.build();
```
