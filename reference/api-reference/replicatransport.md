# ReplicaTransport

In the context of the Internet Computer blockchain, a replica refers to the Internet Computer protocol processes running on a node.

To be able to connect remotely to the Internet Computer canister IC4J implements ReplicaTransport interface over different Java HTTP Client libraries. Developers can choose specific implementation based on their Java application use case.

[ReplicaTransport](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/ReplicaTransport.java) interface currently supports 4 Internet Computer functions. Calls in [ReplicaTransport](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/ReplicaTransport.java) interface are asynchronous and return [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) response type.

```java
public interface ReplicaTransport {
    public CompletableFuture<byte[]> status();
    public CompletableFuture<byte[]> query(Principal canisterId, byte[] envelope);
    public CompletableFuture<byte[]> call(Principal canisterId, byte[] envelope, RequestId requestId);
    public CompletableFuture<byte[]> readState(Principal canisterId, byte[] envelope);
}
```

### Apache HTTP 5 Client transport implementation

Apache HTTP 5 library is robust, stable Java implementation of HTTP protocol. It allows developers to define advanced features like connection pooling.

{% embed url="https://hc.apache.org/httpcomponents-client-5.1.x/index.html" %}

The simplest way how to create [ReplicaTransport](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/ReplicaTransport.java) is to use [ReplicaApacheHttpTransport](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/http/ReplicaApacheHttpTransport.java) create method with the IC URL String as a parameter.&#x20;

```java
ReplicaTransport transport = 
ReplicaApacheHttpTransport.create("http://localhost:8000");
```

For advanced use cases, for example if you want to create Java server type application handling large number of clients and canisters you can define additional parameters.

| Parameter            |                                        |
| -------------------- | -------------------------------------- |
| url                  | Canister URL                           |
| maxTotal             | Maximum total connections              |
| maxPerRoute          | Maximum connections per route          |
| connectionTimeToLive | Time to live for connection in seconds |
| timeout              | Connection timeout in seconds          |

```java
ReplicaTransport transport = 
ReplicaApacheHttpTransport.create("http://localhost:8000", maxTotal, maxPerRoute,
 connectionTimeToLive, int timeout);
```

For even more complex scenarios you can create ReplicaTransport with explicitly defined Apache HTTP Client connection manager [AsyncClientConnectionManager](https://hc.apache.org/httpcomponents-client-5.1.x/current/httpclient5/apidocs/org/apache/hc/client5/http/nio/AsyncClientConnectionManager.html).

```
ReplicaTransport transport = 
ReplicaApacheHttpTransport.create("http://localhost:8000",asyncClientConnectionManager, int timeout);
```

### OkHttp Client transport implementation

For Android development is recommended to use OkHttp Client implementation. OkHttp is an efficient HTTP & HTTP/2 client for Android and Java applications.

{% embed url="https://square.github.io/okhttp" %}

Use [ReplicaOkHttpTransport](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/http/ReplicaOkHttpTransport.java) create method with the IC URL String as a parameter to create OkHttp ReplicaTransport

```java
ReplicaTransport transport = 
ReplicaOkHttpTransport.create("http://localhost:8000");
```

You can also define connection timeout explicitly, if you need to.

```java
ReplicaTransport transport = 
ReplicaOkHttpTransport.create("http://localhost:8000", timeout);
```

### Java 11 HTTP Client transport implementation

From Java version 11 and higher, Oracle significantly improved functionality of Java default HTTP Client. If you want to use this version of the transport, you have to explicitly import it in your Gradle or maven build script. (We do it this way to make core libraries compatible with Java version 1.8)

{% tabs %}
{% tab title="Gradle" %}
```
implementation group: 'org.ic4j', name: 'ic4j-java11transport', version: '0.6.7'
```
{% endtab %}

{% tab title="Maven" %}
```xml
<dependency>
    <groupId>org.ic4j</groupId>
    <artifactId>ic4j-java11transport</artifactId>
    <version>0.6.7</version>
</dependency>
```
{% endtab %}
{% endtabs %}

Use [ReplicaJavaHttpTransport](https://github.com/ic4j/ic4j-java11transport/blob/master/src/main/java/org/ic4j/agent/http/ReplicaJavaHttpTransport.java) create method with the IC URL String as a parameter to create Java 11 ReplicaTransport

```java
ReplicaTransport transport = 
ReplicaJavaHttpTransport.create("http://localhost:8000");
```

You can also define connection timeout explicitly, if you need to.

```java
ReplicaTransport transport = 
ReplicaJavaHttpTransport.create("http://localhost:8000", timeout);
```

###
