# AgentBuilder

To create [Agent](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/Agent.java) Java object we will use [AgentBuilder](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/AgentBuilder.java) build method.&#x20;

Use transport method passing [ReplicaTransport](replicatransport.md) parameter.

```java
new AgentBuilder().transport(replicaTransport);
```

Use identity method passing [Identity](identity.md) parameter.

```java
new AgentBuilder().identity(identity)
```

IngresExpiry provides a default ingress expiry. This is the delta that will be applied  at the time an update or query is made. Use Java [Duration](https://docs.oracle.com/javase/8/docs/api/java/time/Duration.html) type parameter.

```java
new AgentBuilder().ingresExpiry(Duration.ofSeconds(300));
```

And this is how we create Agent object.

```java
Agent agent = new AgentBuilder().transport(replicaTransport)
.identity(identity)
.ingresExpiry(Duration.ofSeconds(300))
.build();
```
