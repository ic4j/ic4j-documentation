# Using IDLArgs

To create the binary form of Candid data in Java or to convert data from candid binary form to Java use the [IDLArgs](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/parser/IDLArgs.java) class.

This is how to create a byte\[] array from a List of [IDLValue](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/parser/IDLValue.java).&#x20;

[IDLValue](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/parser/IDLValue.java) is a wrapped value, consisting of the Java value and the  [IDLType](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/parser/IDLType.java).

```java
List<IDLValue> args = new ArrayList<IDLValue>();
BigInteger intValue = new BigInteger("10000");
args.add(IDLValue.create(intValue));
IDLArgs idlArgs = IDLArgs.create(args);
byte[] payload = idlArgs.toBytes();
```

To convert data from byte\[] array to IDLArgs use the Method **fromBytes**.

```java
IDLArgs outArgs = IDLArgs.fromBytes(output);
```

When dealing with Complex Types, consider defining IDLTypes , a useful way for deserialization.&#x20;

```java
IDLType[] idlTypes = { idlValue.getIDLType() };
IDLArgs outArgs = IDLArgs.fromBytes(output, idlTypes);
```

## IDLType

[IDLType](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/parser/IDLType.java) class is a wrapper for Candid type definition. Use **createType** method to create a Java object.

For simple Candid types use only the [Type](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/types/Type.java) argument.

```java
IDLType idlType = IDLType.createType(Type.INT);
```

When creating VEC or OPT Candid types, the **inner type** needs to  be defined.&#x20;

The inner type can also have **nested types**. &#x20;

```java
IDLType idlArrayType = IDLType.createType(Type.VEC,Type.INT);
IDLType idlOptionalType = IDLType.createType(Type.OPT,Type.INT);
```

When creating the RECORD or VARIANT Candid types, the Type Map needs to be defined.&#x20;

Type Map can also have nested types.

The key in **Type Map** is the [Label](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/types/Label.java) type. **Label** can be _Named, Unnamed or Id type_.

```java
Map<Label, Object> typesMap = new HashMap<Label, Object>();
mapValue.put(Label.createNamedLabel("foo"), IDLType.createType(Type.INT));
IDLType idlType = IDLType.createType(Type.RECORD,typesMap);
```

To create the [IDLType](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/parser/IDLType.java) directly from Java class or object use the helper methods.&#x20;

These will automatically identify the default Candid type for Java class or object.

```java
IDLType idlType = IDLType.createType(Integer.class);
```

```java
BigInteger value = new BigInteger("100000000");
IDLType idlType = IDLType.createType(value);
```

To override **default type** use this variant.

```java
IDLType idlType = IDLType.createType(value, Type.NAT);
```

## IDLValue

To create the IDLValue Java object use the **Method** **Create** functon.&#x20;

This method has several variants, in if the **explicit type** definition is required.

To get the Java object value from IDLValue use the **Method** **getValue** function.

```java
BigInteger value = idlValue.getValue();
```
