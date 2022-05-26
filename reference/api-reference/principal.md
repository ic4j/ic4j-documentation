# Principal

A principal is an entity that can be authenticated by the Internet Computer blockchain. Principals that interact with the Internet Computer blockchain often do so via an identity.

To create a [Principal](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/types/Principal.java) object in Java one of these static methods can be used.&#x20;

1\) To create Principal from Java String object.

```java
Principal principal = Principal.fromString(stringValue);
```

2\) To create Principal from Java byte\[] array object.

```java
Principal principal = Principal.from(byteArrayValue);
```

3\) To create Management Canister Principal.

```java
Principal principal = Principal.managementCanister() ;
```

4\) To create Anonymous Principal.

```java
Principal principal = Principal.anonymous() ;
```

5\) To create Self Authenticating Principal from public key byte\[] array object.

```java
Principal principal = Principal.selfAuthenticating(publicKeyByteArrayValue) ;
```
