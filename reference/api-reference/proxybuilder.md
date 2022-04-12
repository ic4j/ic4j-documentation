# ProxyBuilder

The easiest way how to use IC4J Agent to make calls to the Internet Computer is to use ProxyBuilder. ProxyBuilder will use Java interface with IC4J annotations which represents the Internet Computer canister and its methods and will create proxy object which implements defined  interface. ProxyBuilder will then provide all Candid type serialization and deserialization and also execution of specified canister methods.

Let's call Motoko [canister](https://github.com/ic4j/samples/blob/master/IC4JHelloWorldAdvanced/src/main.mo) with one QUERY and one UPDATE method.

```
actor {
    stable var name = "Me";

    public func greet(value : Text) : async Text {
        name := value;
        return "Hello, " # name # "!";
    };

    public shared query func peek() : async Text {
        return name;
    };    
};
```

The Java Proxy interface for this canister looks like [this](https://github.com/ic4j/samples/blob/master/IC4JHelloWorldAdvanced/src/main/java/org/ic4j/samples/helloworld/HelloWorldProxy.java).

```java
public interface HelloWorldProxy {	
	@UPDATE
	@Name("greet")
	@Waiter(timeout = 30)
	public CompletableFuture<String> greet(@Argument(Type.TEXT)String name);
	
	@QUERY
	@Name("peek")
	public String peek();
}
```

To define if the method is QUERY or UPDATE use [@QUERY](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/QUERY.java) or [@UPDATE](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/UPDATE.java) annotations. The annotation @Name is optional, if not specified, the ProxyBuilder will implicitly use name of the Java method.

For UPDATE method the developer can explicitly define Waiter object with [@Waiter](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/Waiter.java) annotation, which will check state of UPDATE operation in certain interval, defined  by sleep property. The default value is set to 5 seconds. The developer can also define timeout property, until when the Waiter should keep checking the state. Default value is set to 60 seconds. If the annotation is not specified, the ProxyBuilder will automatically set default values.

For method parameters the developer can also set Candid type of the argument with [@Argument ](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/Argument.java)annotation. If this annotation is not defined, the ProxyBuilder will use default Java to Candid [mapping](supported-types.md).

Now we can create proxy object in Java [code](https://github.com/ic4j/samples/blob/master/IC4JHelloWorldAdvanced/src/main/java/org/ic4j/samples/helloworld/Main.java).

```java
Agent agent = new AgentBuilder().transport(transport).identity(identity).build();			
			
HelloWorldProxy helloWorldProxy = ProxyBuilder.create(agent, Principal.fromString(icCanister))
					.getProxy(HelloWorldProxy.class);
```

ProxyBuilder create method will take 2 arguments, agent and principal.  getProxy method will use proxy interface class as an argument.

The call to the canister is now as simple as calling any other Java function.&#x20;

```java
String output = helloWorldProxy.peek();

String value = "world";			
CompletableFuture<String> proxyResponse = helloWorldProxy.greet(value);
output = proxyResponse.get();
```

UPDATE function call is executed asynchronously, returning Java [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) result.

The full source code of this sample can be found [here](https://github.com/ic4j/samples/tree/master/IC4JHelloWorldAdvanced).&#x20;

Developers can optionally use additional annotations.

```java
@Agent(identity = @Identity(type = IdentityType.BASIC, pem_file = "/cert/Ed25519_identity.pem"), transport = @Transport(url = "http://localhost:8001"))
@Canister("rrkah-fqaaa-aaaaa-aaaaq-cai")
@EffectiveCanister("rrkah-fqaaa-aaaaa-aaaaq-cai")
public interface LoanBroker {
	@UPDATE
	@Name("apply")
	@Waiter(timeout = 30)
	@ResponseClass(LoanOffer.class)
	public CompletableFuture<LoanOffer> apply(@Argument(Type.RECORD)LoanApplication application);
}
```

Use [@Agent](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/Agent.java) annotation if you want to define Identity and Transport properties directly in the proxy interface.

Use [@Canister](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/Canister.java) annotation to define canister id.

Use [@EffectiveCanister](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/EffectiveCanister.java) annotation to define effective canister id.

If you are using complex type response and you need to define Java class you need to deserialize to use [@ResponseClass](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/ResponseClass.java) annotation.
