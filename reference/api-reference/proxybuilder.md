# ProxyBuilder

Using ProxyBuilder is the easiest way to make calls to the Internet Computer using the IC4J Agent. ProxyBuilder will use the Java interface with IC4J annotations which represent the Internet Computer Canister.

The Internet Computer Canister and its Methods will create a proxy object which implements the defined interface.&#x20;

ProxyBuilder will then provide all Candid type serialization and deserialization steps and the execution of specified canister methods.

Here is an example using Motoko [canister](https://github.com/ic4j/samples/blob/master/IC4JHelloWorldAdvanced/src/main.mo) with one QUERY and one UPDATE method.

{% code title="main.mo" %}
```javascript
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
{% endcode %}

The Java Proxy interface for this canister looks like [this](https://github.com/ic4j/samples/blob/master/IC4JHelloWorldAdvanced/src/main/java/org/ic4j/samples/helloworld/HelloWorldProxy.java).

{% code title="HelloWorldProxy.java" %}
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
{% endcode %}

To define if the method is QUERY or UPDATE use [@QUERY](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/QUERY.java) or [@UPDATE](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/UPDATE.java) annotations.&#x20;

The annotation @Name is optional, but if not specified, the ProxyBuilder will implicitly use the name of the Java method.

For the UPDATE method the developer can explicitly define the Waiter object with [@Waiter](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/Waiter.java) annotation, which will check the state of the UPDATE operation in certain intervals, defined  by the sleep property. The default value for the sleep property is set to 5 seconds.&#x20;

The developer can also define the timeout property, to specify when the Waiter should keep checking the state.&#x20;

The default value for the timeout property is set to 60 seconds. If the annotation is not specified, the ProxyBuilder will automatically set default values.

For Method parameters the developer can also set a Candid type of the argument with the [@Argument ](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/Argument.java)annotation. If this annotation is not defined, the ProxyBuilder will use the default Java to Candid [mapping](supported-types.md).

Here is an example of how to create a proxy object in Java [code](https://github.com/ic4j/samples/blob/master/IC4JHelloWorldAdvanced/src/main/java/org/ic4j/samples/helloworld/Main.java).

{% code title="Main.java" %}
```java
Agent agent = new AgentBuilder().transport(transport).identity(identity).build();			
			
HelloWorldProxy helloWorldProxy = ProxyBuilder.create(agent, Principal.fromString(icCanister))
					.getProxy(HelloWorldProxy.class);rr
```
{% endcode %}

The **ProxyBuilder Create Method** will accept 2 arguments, **Agent and Principal.**&#x20;

The **getProxy Method** will use the **proxy interface class** as an argument.&#x20;

The call to the canister can now be completed by calling any other Java function.&#x20;

{% code title="" %}
```java
String output = helloWorldProxy.peek();

String value = "world";			
CompletableFuture<String> proxyResponse = helloWorldProxy.greet(value);
output = proxyResponse.get();
```
{% endcode %}

The **UPDATE** function call is executed asynchronously, returning  the Java [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) result.

The full source code of this sample can be found [here](https://github.com/ic4j/samples/tree/master/IC4JHelloWorldAdvanced).

{% code title="LoanBroker.java" %}
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
{% endcode %}

Developers can optionally use the following additional annotations.

Use the  [@Agent](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/Agent.java) annotation to define Identity and Transport properties directly in the proxy interface.

Use the  [@Canister](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/Canister.java) annotation to define canister id.

Use the  [@EffectiveCanister](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/annotations/EffectiveCanister.java) annotation to define effective canister id.

If the **Complex Type Response** is being used with Java class needing to be defined, and deserializing is necessary,  use the @ResponseClass annotation.&#x20;
