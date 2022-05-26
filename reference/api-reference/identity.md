# Identity

IC4J Java Agent currently supports 3 different identity mechanisms. To learn more about the Internet Computer identity mechanisms refer to the Dfinity [documentation](https://smartcontracts.org/docs/interface-spec/index.html).

IC4J Agent uses the open source Java cryptography library [Bouncy Castle ](https://www.bouncycastle.org/java.html)in its implementation.&#x20;

If [BasicIndentity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/BasicIdentity.java) or [Secp256k1Identity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/Secp256k1Identity.java) is being used, define **Bouncy Castle** as the Java security provider in the code, before an Identity is created.

```
```

## AnonymousIdentity

AnonymousIdentity is the default mechanism in the IC4J Agent ; this means that if the identity is not specified explicitly , [AnonymousIdentity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/AnonymousIdentity.java) will be assigned implicitly.

To explicitly create the [AnonymousIdentity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/AnonymousIdentity.java) object, the **AnonymousIdentity Constructor** can be used.

```java
Identity identity = new AnonymousIdentity();
```

## BasicIdentity (ED25519)

The Internet Computer provides support for [ED25519](https://ed25519.cr.yp.to/) signatures. The [**dfx**](https://smartcontracts.org/docs/developers-guide/cli-reference/dfx-parent.html) **tool** can be used to generate the identity **PEM file**.

```
dfx identity new alice
cp ~/.config/dfx/identity/xxx/identity.pem alice.pem
```

Either the Java [Reader ](https://docs.oracle.com/javase/8/docs/api/java/io/Reader.html)or Java[ Path](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html) can be used to read the ED22219 PEM resource to create the [BasicIdentity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/BasicIdentity.java) object.

```java
Reader sourceReader = new InputStreamReader(Main.class.getClassLoader().getResourceAsStream(ED25519_IDENTITY_FILE));
identity = BasicIdentity.fromPEMFile(sourceReader);
```

```java
Path path = Paths.get(getClass().getClassLoader().getResource(ED25519_IDENTITY_FILE).getPath());
Identity identity = BasicIdentity.fromPEMFile(path);
```

Another option is to use Java [KeyPair](https://docs.oracle.com/javase/8/docs/api/java/security/KeyPair.html) as an input parameter.

```java
KeyPair keyPair = KeyPairGenerator.getInstance("Ed25519").generateKeyPair();
Identity identity = BasicIdentity.fromKeyPair(keyPair);
```

The Java byte\[] array can also be used as an input parameter.

```java
byte[] input;
Identity identity = BasicIdentity.fromPEM(input);
```

## Secp256k1Identity

The Internet Computer also supports [Secp256k1](https://en.bitcoin.it/wiki/Secp256k1) signatures commonly used with Bitcoin or Ethereum.

Either Java [Reader ](https://docs.oracle.com/javase/8/docs/api/java/io/Reader.html)or Java[ Path](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html) can be used to read the Secp256k1 PEM resource to create the [Secp256k1Identity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/Secp256k1Identity.java) object.

```java
Reader sourceReader = new InputStreamReader(Main.class.getClassLoader().getResourceAsStream(ED25519_IDENTITY_FILE));
identity = Secp256k1Identity.fromPEMFile(sourceReader);
```

```java
Path path = Paths.get(getClass().getClassLoader().getResource(ED25519_IDENTITY_FILE).getPath());
Identity identity = Secp256k1Identity.fromPEMFile(path);
```

Another option is to use Java [KeyPair](https://docs.oracle.com/javase/8/docs/api/java/security/KeyPair.html) as an input parameter.

```java
Identity identity = Secp256k1Identity.fromKeyPair(keyPair);
```

To see a fully functional Java sample with all 3 Identity mechanisms refer to this Github [sample](https://github.com/ic4j/samples/tree/master/IC4JIdentitySample).
