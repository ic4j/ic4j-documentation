# ReplicaTransport

In the context of the Internet Computer blockchain, a **Replica** refers to the Internet Computer protocol processes running on a node.

To be able to connect remotely to the Internet Computer Canister IC4J implements ReplicaTransport interface over different Java HTTP Client libraries. Developers can choose specific implementations based on their Java application use cases.

[ReplicaTransport](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/ReplicaTransport.java) interface currently supports 4 Internet Computer functions.&#x20;

Calls in [ReplicaTransport](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/ReplicaTransport.java) interface are asynchronous and return the [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) response type.

```java
public interface ReplicaTransport {
    public CompletableFuture<byte[]> status();
    public CompletableFuture<byte[]> query(Principal canisterId, byte[] envelope);
    public CompletableFuture<byte[]> call(Principal canisterId, byte[] envelope, RequestId requestId);
    public CompletableFuture<byte[]> readState(Principal canisterId, byte[] envelope);
}
```

## Apache HTTP 5 Client transport implementation

The **Apache HTTP 5 library** is a robust, stable Java implementation of the HTTP protocol. It allows developers to define advanced features like connection pooling.

{% embed url="https://hc.apache.org/httpcomponents-client-5.1.x/index.html" %}

The simplest way to create [ReplicaTransport](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/ReplicaTransport.java) is to use [ReplicaApacheHttpTransport](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/http/ReplicaApacheHttpTransport.java) to create the Method with the **IC URL String** as a parameter.&#x20;

```java
ReplicaTransport transport = 
ReplicaApacheHttpTransport.create("http://localhost:4943/");
```

For advanced use cases, for example, to create Java server type applications handling a large number of clients and canisters, additional parameters can be defined.

<table><thead><tr><th width="206.28690807799444">Parameter</th><th></th></tr></thead><tbody><tr><td>url</td><td>Canister URL</td></tr><tr><td>maxTotal</td><td>Maximum total connections</td></tr><tr><td>maxPerRoute</td><td>Maximum connections per route</td></tr><tr><td>connectionTimeToLive</td><td>Time to live for connection in seconds</td></tr><tr><td>timeout</td><td>Connection timeout in seconds</td></tr></tbody></table>

```java
ReplicaTransport transport = 
ReplicaApacheHttpTransport.create("http://localhost:4943/", maxTotal, maxPerRoute,
 connectionTimeToLive, int timeout);
```

For even more complex scenarios ReplicaTransport can be created with the explicitly defined Apache HTTP Client connection manager [AsyncClientConnectionManager](https://hc.apache.org/httpcomponents-client-5.1.x/current/httpclient5/apidocs/org/apache/hc/client5/http/nio/AsyncClientConnectionManager.html).

```
ReplicaTransport transport = 
ReplicaApacheHttpTransport.create("http://localhost:4943/",asyncClientConnectionManager, int timeout);
```

## OkHttp Client transport implementation

For Android development it is recommended to use the **OkHttp Client Implementation**.&#x20;

OkHttp is an efficient HTTP & HTTP/2 client for Android and Java applications.

{% embed url="https://square.github.io/okhttp" %}

Use [ReplicaOkHttpTransport](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/http/ReplicaOkHttpTransport.java) to create the Method with the **IC URL String** as a parameter to create OkHttp ReplicaTransport

```java
ReplicaTransport transport = 
ReplicaOkHttpTransport.create("http://localhost:4943/");
```

If needed, the **Connection Timeout** can be explicitly defined : &#x20;

```java
ReplicaTransport transport = 
ReplicaOkHttpTransport.create("http://localhost:4943/", timeout);
```

## Java 11 HTTP Client transport implementation

From Java version 11 and higher, Oracle significantly improved functionality of Java default HTTP Client.&#x20;

To make core libraries compatible with Java version 1.8, it is recommended that this version of **transport**  is explicitly imported in the Graven or Maven build script.&#x20;

{% tabs %}
{% tab title="Gradle" %}
```
implementation 'org.ic4j:ic4j-java11transport:0.7.0'
```
{% endtab %}

{% tab title="Maven" %}
```xml
<dependency>
    <groupId>org.ic4j</groupId>
    <artifactId>ic4j-java11transport</artifactId>
    <version>0.7.0</version>
</dependency>
```
{% endtab %}
{% endtabs %}

Use [ReplicaJavaHttpTransport](https://github.com/ic4j/ic4j-java11transport/blob/master/src/main/java/org/ic4j/agent/http/ReplicaJavaHttpTransport.java) to create the Method with the **IC URL String** as a parameter to create **Java 11 ReplicaTransport**

```java
ReplicaTransport transport = 
ReplicaJavaHttpTransport.create("http://localhost:4943/");
```

If needed the connection timeout can be defined explicitly.

```java
ReplicaTransport transport = 
ReplicaJavaHttpTransport.create("http://localhost:4943/", timeout);
```

###
