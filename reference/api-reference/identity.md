# Identity

IC4J Java Agent currently supports 3 different identity mechanisms. To learn more about the Internet Computer identity please visit Dfinity [documentation](https://smartcontracts.org/docs/interface-spec/index.html).

IC4J Agent is using open source Java cryptography library [Bouncy Castle ](https://www.bouncycastle.org/java.html)in our implementation.&#x20;

If you are using [BasicIndentity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/BasicIdentity.java) or [Secp256k1Identity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/Secp256k1Identity.java), define Bouncy Castle as your Java security provider in your code, before you create an Identity.

```
```

### AnonymousIdentity

AnonymousIdentity is default mechanism in IC4J Agent, that means if you do not specify  Identity explicitly, [AnonymousIdentity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/AnonymousIdentity.java) will be assigned implicitly.

But if you want to explicitly create [AnonymousIdentity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/AnonymousIdentity.java) object, you can use AnonymousIdentity constructor.

```java
Identity identity = new AnonymousIdentity();
```

### BasicIdentity (ED25519)

The Internet Computer provides support for [ED25519](https://ed25519.cr.yp.to) signatures. You can use [dfx](https://smartcontracts.org/docs/developers-guide/cli-reference/dfx-parent.html) tool to generate identity PEM file.

```
dfx identity new alice
cp ~/.config/dfx/identity/xxx/identity.pem alice.pem
```

You can use either Java [Reader ](https://docs.oracle.com/javase/8/docs/api/java/io/Reader.html)or Java[ Path](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html) to read your ED22219 PEM resource and create [BasicIdentity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/BasicIdentity.java) object.

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

You can also use Java byte\[] array as an input parameter.

```java
byte[] input;
Identity identity = BasicIdentity.fromPEM(input);
```

### Secp256k1Identity

The Internet Computer also supports [Secp256k1](https://en.bitcoin.it/wiki/Secp256k1) signatures commonly used with Bitcoin or Ethereum.

You can use either Java [Reader ](https://docs.oracle.com/javase/8/docs/api/java/io/Reader.html)or Java[ Path](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html) to read your Secp256k1 PEM resource and create [Secp256k1Identity](https://github.com/ic4j/ic4j-agent/blob/master/src/main/java/org/ic4j/agent/identity/Secp256k1Identity.java) object.

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

To see fully functional Java sample with all 3 Identity mechanisms in our Github [sample](https://github.com/ic4j/samples/tree/master/IC4JIdentitySample).
