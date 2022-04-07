# Quick Start

### Make your first Java call to the IC canister

To create your first IC canister, follow instructions from Dfinity Quick Start.

{% embed url="https://smartcontracts.org/docs/quickstart/2-quickstart.html" %}
Dfinity Quick Start
{% endembed %}

You can either use local installation of the Canister SDK or use Motoko Playground.

{% embed url="https://m7sm4-2iaaa-aaaab-qabra-cai.raw.ic0.app" %}
Motoko Playground
{% endembed %}

If you create local project, it will implicitly create the first Motoko file [**main.mo**](https://github.com/ic4j/samples/blob/master/IC4JHelloWorld/src/main.mo). The code is very simple HelloWorld application with one method named **greet**_._

```
actor {
  public func greet(name : Text) : async Text {
    return "Hello, " # name # "!";
  };
};
```

To run this canister code in Motoko Playground just copy and paste this source to the Playground editor and click Deploy button.

We can start building the first Java application, that will invoke **greet** method. The source of IC4JHelloWorld project can be found [here](https://github.com/ic4j/samples/tree/master/IC4JHelloWorld).

For your Java project you can use either Gradle or Maven build. To include IC4J support in your project include Gradle or Maven dependencies.&#x20;

{% tabs %}
{% tab title="Gradle" %}
```markup
implementation 'org.ic4j:ic4j-agent:0.6.7'
implementation 'org.ic4j:ic4j-candid:0.6.6'
```
{% endtab %}

{% tab title="Maven" %}
```xml
<dependency>
  <groupId>org.ic4j</groupId>
  <artifactId>ic4j-agent</artifactId>
  <version>0.6.7</version>
</dependency>
<dependency>
  <groupId>org.ic4j</groupId>
  <artifactId>ic4j-candid</artifactId>
  <version>0.6.6</version>
</dependency>
```
{% endtab %}
{% endtabs %}

Now we can start writing Java code. The easiest way how to communicate with the Internet Computer canister is to use ProxyBuilder. The ProxyBuilder creates Canister Java proxy object based on Java interface with Canister annotations. You can find full source of this interface [here](https://github.com/ic4j/samples/blob/master/IC4JHelloWorld/src/main/java/org/ic4j/samples/helloworld/HelloWorldProxy.java).

```java
public interface HelloWorldProxy {	
	@UPDATE
	@Name("greet")
	@Waiter(timeout = 30)
	public CompletableFuture<String> greet(@Argument(Type.TEXT)String name);
}
```

As you can see we can define what type is the Canister method (UPDATE or QUERY), name of the method and for UPDATE method also define Waiter properties. For method arguments we can also define Candid type.

First we have to create to create ReplicaTransport object using URL to your Canister (either local or remote). The we use AgentBuilder to create Agent object.&#x20;

to create Canister Proxy object use ProxyBuilder _create_ method with agent and Canister Principal arguments and then _getProxy_ method passing Java proxy class.

The full source can be found [here](https://github.com/ic4j/samples/blob/master/IC4JHelloWorld/src/main/java/org/ic4j/samples/helloworld/Main.java).

```java
ReplicaTransport transport = ReplicaApacheHttpTransport.create(icLocation);
Agent agent = new AgentBuilder().transport(transport).build();			
HelloWorldProxy helloWorldProxy = ProxyBuilder.create(agent, Principal.fromString(icCanister))
					.getProxy(HelloWorldProxy.class);
String value = "world";		
CompletableFuture<String> proxyResponse = helloWorldProxy.greet(value);			
String output = proxyResponse.get();
```

As you can see, to call the Internet Computer canister you will need 2 properties, location URL and Canister Id. This sample is reading those properties from [_application.properties_](https://github.com/ic4j/samples/blob/master/IC4JHelloWorld/src/main/resources/application.properties) file.

```java
ic.location=https://m7sm4-2iaaa-aaaab-qabra-cai.raw.ic0.app/
ic.canister=yaku6-4iaaa-aaaab-qacfa-cai
```

Replace those properties with ones from your deployed canister, either local or remote.

Now you can just build the java project and run. Use Gradle script [build.gradle](https://github.com/ic4j/samples/blob/master/IC4JHelloWorld/build.gradle). (This build requires Java 1.8, if you are using different version, please make modification in the script). This build script creates fat jar with all required dependencies.

```
gradle build
```

Then run with java.

```
java -jar build/libs/ic4j-sample-helloworld-0.6.6.jar
```

You should see result like this.

```
[main] INFO org.ic4j.samples.helloworld.Main - Hello, world!
```
