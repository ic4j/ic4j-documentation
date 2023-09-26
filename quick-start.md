# Quick Start

### Make your first Java call to the IC canister

To create your first IC canister, follow instructions from the Dfinity Quick Start location.

{% embed url="https://smartcontracts.org/docs/quickstart/2-quickstart.html" %}
Dfinity Quick Start
{% endembed %}

You can either use local installation of the Canister SDK or use Motoko Playground.

{% embed url="https://m7sm4-2iaaa-aaaab-qabra-cai.raw.ic0.app" %}
Motoko Playground
{% endembed %}

If you create local project, it will implicitly create the first Motoko file [**main.mo**](https://github.com/ic4j/samples/blob/master/IC4JHelloWorld/src/main.mo). The code is a very simple HelloWorld application with one method named **greet**_._

{% code title="main.mo" %}
```javascript
actor {
  public func greet(name : Text) : async Text {
    return "Hello, " # name # "!";
  };
};
```
{% endcode %}

To run this canister code in Motoko Playground just copy and paste this source to the Playground editor and click the **Deploy** button.

We can start building the first Java application, that will invoke **greet** method. The source of IC4JHelloWorld project can be found [here](https://github.com/ic4j/samples/tree/master/IC4JHelloWorld).

For your Java project you can use either Gradle or Maven build. To include IC4J support in your project include Gradle or Maven dependencies.&#x20;

{% tabs %}
{% tab title="Gradle" %}
```markup
implementation 'org.ic4j:ic4j-agent:0.6.19.6'
implementation 'org.ic4j:ic4j-candid:0.6.19.5'
```
{% endtab %}

{% tab title="Maven" %}
```xml
<dependency>
  <groupId>org.ic4j</groupId>
  <artifactId>ic4j-agent</artifactId>
  <version>0.6.19.6</version>
</dependency>
<dependency>
  <groupId>org.ic4j</groupId>
  <artifactId>ic4j-candid</artifactId>
  <version>0.6.19.5</version>
</dependency>
```
{% endtab %}
{% endtabs %}

Now we can start writing Java code. The easiest way to communicate with the Internet Computer **Canister** is to use **ProxyBuilder**.&#x20;

The ProxyBuilder module creates the Java proxy object Canister  based on Java interface with Canister annotations. You can find full source of this interface [here](https://github.com/ic4j/samples/blob/master/IC4JHelloWorld/src/main/java/org/ic4j/samples/helloworld/HelloWorldProxy.java).

{% code title="HelloWorldProxy.java" %}
```java
public interface HelloWorldProxy {	
	@UPDATE
	@Name("greet")
	@Waiter(timeout = 30)
	public CompletableFuture<String> greet(@Argument(Type.TEXT)String name);
}
```
{% endcode %}

Next, define the **Type of Method** for the Canister (UPDATE or QUERY), the **Name** of the method and define **Waiter** properties for **UPDATE** method.&#x20;

For method arguments we can also define **Candid** type.

First we have to create the **ReplicaTransport** object using the URL to your Canister (either local or remote). The we use AgentBuilder to create the **Agent Object**.&#x20;

To create the **Canister Proxy** object use ProxyBuilder _create_ the method with the agent and the Canister Principal arguments and then _getProxy_ method passing Java proxy class.

The full source can be found [here](https://github.com/ic4j/samples/blob/master/IC4JHelloWorld/src/main/java/org/ic4j/samples/helloworld/Main.java).

{% code title="Main.java" %}
```java
ReplicaTransport transport = ReplicaApacheHttpTransport.create(icLocation);
Agent agent = new AgentBuilder().transport(transport).build();			
HelloWorldProxy helloWorldProxy = ProxyBuilder.create(agent, Principal.fromString(icCanister))
					.getProxy(HelloWorldProxy.class);
String value = "world";		
CompletableFuture<String> proxyResponse = helloWorldProxy.greet(value);			
String output = proxyResponse.get();
```
{% endcode %}

Next, to call the Internet Computer Canister you will need 2 properties, **location URL** and **Canister ID.**&#x20;

This is an example to read those properties from the [_application.properties_](https://github.com/ic4j/samples/blob/master/IC4JHelloWorld/src/main/resources/application.properties) file.

{% code title="application.properties" %}
```java
ic.location=https://m7sm4-2iaaa-aaaab-qabra-cai.raw.ic0.app/
ic.canister=yaku6-4iaaa-aaaab-qacfa-cai
```
{% endcode %}

Replace those properties with ones from your deployed canister, either local or remote.

The **UPDATE** call will run asynchronously and return the Java [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) object.

Next, the Java project can be built and run using **Gradle Script** [build.gradle](https://github.com/ic4j/samples/blob/master/IC4JHelloWorld/build.gradle). (_**This build requires Java 1.8;**_ if you are using a different version, make the necessary modifications in the script).&#x20;

This build script creates **Fat Ja**r with all the required dependencies.

```
gradle build
```

Next run with Java.

```
java -jar build/libs/ic4j-sample-helloworld-0.6.19.jar
```

The output should look like this.

```
[main] INFO org.ic4j.samples.helloworld.Main - Hello, world!
```
