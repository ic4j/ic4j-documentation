# XML DOM Serializer and Deserializer

Use [DOMSerializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/dom/DOMSerializer.java) and [DOMDeserializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/dom/DOMDeserializer.java) to serialize and deserialize [Java XML DOM](https://docs.oracle.com/javase/8/docs/api/org/w3c/dom/package-summary.html) object to and from the Candid payload of type RECORD.&#x20;

A fully functional example using DOMSerializer and DOMDeserializer can be found [here](https://github.com/ic4j/samples/tree/master/IC4JXMLSample).

This example uses Motoko to call the canister [main.mo](https://github.com/ic4j/samples/blob/master/IC4JXMLSample/src/main.mo). The canister uses 2 complex types, **LoanApplication** and **LoanOffer.**

{% code title="main.mo" %}
```javascript
// Loan Application
public type LoanApplication = {
    id: Nat;
    firstname: Text;
    lastname: Text;
    zipcode: Text;
    ssn: Text;
    amount: Float;
    term: Nat16;
    created: Int;
 };

// Loan Offer
public type LoanOffer = {
    providerid: Principal;
    providername: Text;
    applicationid: Nat;
    apr: Float;
    created: Int;
};
```
{% endcode %}

The canister has 2 methods:  **apply** and **getOffers.**

{% code title="main.mo" %}
```javascript
public shared (msg) func apply(input : LoanApplication) : async LoanOffer { 
       counter += 1;
        Debug.print("Loan Application for user " #Principal.toText(msg.caller));
        
        let offer  : LoanOffer = {
            providerid = Principal.fromActor(this);
            providername = "Loan Provider";
            applicationid = counter;
            apr = 3.14;
            created = Time.now();
        };

        var userOffers :  ?Offers<LoanOffer> = offers.get(msg.caller);

        switch userOffers {
            case (null) { var userOffer : Offers<LoanOffer> = Buffer.Buffer(0); userOffer.add(offer);  offers.put(msg.caller, userOffer)};
            case (?userOffer) { userOffer.add(offer); };
        };
        return offer;
};

public query (msg) func getOffers() : async [LoanOffer] {
        var userOffers :  ?Offers<LoanOffer> = offers.get(msg.caller);

        switch userOffers {
            case (null) { return [] };
            case (?userOffer) { return userOffer.toArray() };
        };
};
```
{% endcode %}

The example uses the file with [XML LoanApplication](https://github.com/ic4j/samples/blob/master/IC4JXMLSample/src/resources/LoanApplication.xml) payload as an input.&#x20;

{% code title=" LoanApplication.xml" %}
```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<data xmlns="http://ic4j.org/samples" xmlns:candid="http://ic4j.org/candid" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" candid:type="RECORD">
<amount candid:name="amount" candid:type="FLOAT64" xsi:type="xsd:double">20000.00</amount>
<term candid:name="term" candid:type="NAT16" xsi:type="xsd:unsignedShort">24</term>
<created candid:name="created" candid:type="INT" xsi:type="xsd:integer">0</created>
<id candid:name="id" candid:type="NAT" xsi:type="xsd:positiveInteger">0</id>
<firstname candid:name="firstname" candid:type="TEXT" xsi:type="xsd:string">John</firstname>
<lastname candid:name="lastname" candid:type="TEXT" xsi:type="xsd:string">Doe</lastname>
<zipcode candid:name="zipcode" candid:type="TEXT" xsi:type="xsd:string">99999</zipcode>
<ssn candid:name="ssn" candid:type="TEXT" xsi:type="xsd:string">111-11-1111</ssn>
</data>

```
{% endcode %}

Next, create IDLValue using the **DOMSerializer create** method.&#x20;

The Serializer input is the variable type DOM [Element](https://docs.oracle.com/javase/8/docs/api/org/w3c/dom/Element.html).

{% code title="Main.java" %}
```java
Element xmlValue = readNode(LOAN_APPLICATION_FILE);
		
IDLValue idlValue = IDLValue.create(xmlValue, DOMSerializer.create());
List<IDLValue> idlArgs = new ArrayList<IDLValue>();
idlArgs.add(idlValue);

byte[] buf = IDLArgs.create(idlArgs).toBytes();
```
{% endcode %}

To be able to properly map names and values to Candid name types for DOMDeserializer, declare the [IDLType](../use-idlargs.md#idltype) structure as follows:

{% code title="Main.java" %}
```java
Map<Label,IDLType> offerRecord = new TreeMap<Label,IDLType>();
offerRecord.put(Label.createNamedLabel("providerid"), IDLType.createType(Type.PRINCIPAL));
offerRecord.put(Label.createNamedLabel("providername"), IDLType.createType(Type.TEXT));
offerRecord.put(Label.createNamedLabel("applicationid"), IDLType.createType(Type.NAT));	
offerRecord.put(Label.createNamedLabel("apr"), IDLType.createType(Type.FLOAT64));		
offerRecord.put(Label.createNamedLabel("created"), IDLType.createType(Type.INT));
		
IDLType resultIdlType =  IDLType.createType(Type.RECORD, offerRecord);	
```
{% endcode %}

Use[ UpdateBuilder](../querybuilder-and-updatebuilder.md#updatebuilder), [QueryBuilder](../querybuilder-and-updatebuilder.md#querybuilder) or[ Raw Methods](../using-raw-methods.md) to call the Canister and deserialize output to DOM [Element](https://docs.oracle.com/javase/8/docs/api/org/w3c/dom/Element.html).  Function **rootElement** is used to define root element of the XML structure. To set [Candid XML Attributes](xml-dom-serializer-and-deserializer.md#undefined) in XML output use **setAttributes** function with **true** value.

{% code title="Main.java" %}
```java
CompletableFuture<byte[]> response = UpdateBuilder.create(agent,Principal.fromString(icCanister), "apply").arg(buf).callAndWait(Waiter.create(60, 5));
		
byte[] output = response.get();
Element  xmlResult = IDLArgs.fromBytes(output).getArgs().get(0)
		.getValue(DOMDeserializer.create(resultIdlType)
		.rootElement("http://ic4j.org/samples", "data").setAttributes(true), Element.class);
```
{% endcode %}

By default, DOMDeserializer generates **qualified** XML document with namespaces. To generate **unqualified** XML document use DOMDeserializer function **setQualified** with **false** value.

## XSI Types

DOMSerializer can use XML XSI attribute to convert XML value to specific primitive Candid type.&#x20;

```xml
<data xmlns="http://ic4j.org/samples" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
<amount xsi:type="xsd:double">20000.00</amount>
<term xsi:type="xsd:unsignedShort">24</term>
</data>
```

XSI XML namespace is **http://www.w3.org/2001/XMLSchema-instance**. Type is defined as XML Schema type [http://www.w3.org/2001/XMLSchema](http://www.w3.org/2001/XMLSchema).

Here is mapping table between Candid and XML Schema.



| Candid    | XML Schema     |
| --------- | -------------- |
| bool      | boolean        |
| int       | integer        |
| int8      | byte           |
| int16     | short          |
| int32     | int            |
| int64     | long           |
| nat       | posiiveInteger |
| nat8      | unsignedByte   |
| nat16     | unsignedShort  |
| nat32     | unsignedInt    |
| nat64     | unsignedLong   |
| float32   | float          |
| float64   | double         |
| text      | string         |
| principal | ID             |

## Using Candid XML Attributes

The developer can also use Candid XML attributes **name** and **type** to define Candid name and type. If XML document is qualified then it requires candid namespace definition **http://ic4j.org/candid.**

```xml
<data xmlns="http://ic4j.org/samples" xmlns:candid="http://ic4j.org/candid" candid:type="RECORD">
<amount candid:name="amount" candid:type="FLOAT64">20000.00</amount>
<term candid:name="term" candid:type="NAT16">24</term>
</data>
```

## Handling XML Arrays

By default, DOMSerializer and DOMDeserializer will assume that name of array item is **item**. To modify this name use function **arrayItem** in DOMSerializer and DOMDeserializer.
