# Principal

A principal is an entity that can be authenticated by the Internet Computer blockchain. Principals that interact with the Internet Computer blockchain often do so via an identity.

To create Principal object in Java you can use one of these static methods.&#x20;

Create Principal from Java String object.

```java
Principal principal = Principal.fromString(stringValue);
```

Create Principal from Java byte\[] array object.

```java
Principal principal = Principal.from(byteArrayValue);
```

Create Management Canister Principal.

```java
Principal principal = Principal.managementCanister() ;
```

Create Anonymous Principal.

```java
Principal principal = Principal.anonymous() ;
```

Create Self Authenticating Principal from public key byte\[] array object.

```java
Principal principal = Principal.selfAuthenticating(publicKeyByteArrayValue) ;
```
