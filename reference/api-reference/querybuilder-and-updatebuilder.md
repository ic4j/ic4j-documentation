# QueryBuilder and UpdateBuilder

Another option to call the Internet Computer canisters from Java is to use **QueryBuilder** and **UpdateBuilder**.&#x20;

Use these options if direct manipulation with Candid data is required or there is a requirement for dynamic invocation.

Create byte\[] array binary Candid payload as an input argument using [IDLArgs](using-idlargs.md).

```java
List<IDLValue> args = new ArrayList<IDLValue>();
BigInteger intValue = new BigInteger("10000");
args.add(IDLValue.create(intValue));
IDLArgs idlArgs = IDLArgs.create(args);
byte[] payload = idlArgs.toBytes();
```

## QueryBuilder

[QueryBuilder](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/QueryBuilder.java) **Method create** has 3 arguments, agent, canister id principal and method name.&#x20;

Optionally, the **expiration time** can be set using Methods **expireAfter** or **expireAt.**&#x20;

Pass binary payload as a parameter of **arg** method and execute using **Method** **call**.

```java
CompletableFuture<byte[]> response = QueryBuilder
	.create(agent, Principal.fromString(canisterid), "echoInt")
	.expireAfter(Duration.ofMinutes(3))
	.arg(payload)
	.call();
```

## UpdateBuilder

[UpdateBuilder](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/UpdateBuilder.java) uses very similar syntax to Method **create,** but has one extra method, **callAndWait ,**if explicit [Waiter](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/Waiter.java) definition is required.

```java
CompletableFuture<byte[]> response = UpdateBuilder
	.create(agent, Principal.fromString(canisterid), "greet")
	.expireAfter(Duration.ofMinutes(3))
	.arg(payload)
	.callAndWait(Waiter.create(60, 5));
```

Use this to convert response payload to Java objects from binary Candid response payload.

```java
byte[] output = response.get();
IDLArgs outArgs = IDLArgs.fromBytes(output);
```
