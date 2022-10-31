# XML JAXB Serializer and Deserializer

Use [JAXBSerializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/jaxb/JAXBSerializer.java) and [JAXBDeserializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/jaxb/JAXBDeserializer.java) to serialize and deserialize [Java XML JAXB ](https://docs.oracle.com/javase/tutorial/jaxb/intro/index.html)object to and from the Candid payload of type RECORD.&#x20;

Java Architecture for XML Binding (JAXB) is a standard that defines an API for reading and writing Java objects to and from XML documents. It applies a lot of defaults, thus making reading and writing of XML via Java relatively easy.

With Java releases lower than Java 11, JAXB was part of the JVM and you could use it directly without defining additional libaries.

As of Java 11, JAXB is not part of the JRE anymore and you need to configure the relevant libraries via your dependency management system, for example Maven or Gradle.&#x20;

A fully functional example using JAXBSerializer and JAXBDeserializer can be found [here](https://github.com/ic4j/samples/tree/master/IC4JJAXBSample).

This example uses Motoko to call the canister [main.mo](https://github.com/ic4j/samples/blob/master/IC4JJAXBSample/src/main.mo). The canister uses 2 complex types, **LoanApplication** and **LoanOffer.**

{% code title="main.mo" %}
```javascript
  // Loan Application
public type LoanApplication = {
    id: Int;
    firstname: Text;
    lastname: Text;
    zipcode: Text;
    ssn: Text;
    amount: Float;
    term: Int16;
    created: Int;
 };

 // Loan Offer
public type LoanOffer = {
    providerid: Text;
    providername: Text;
    applicationid: Int;
    apr: Float;
    created: Int;
};
```
{% endcode %}

The canister has one method:  **apply.**

{% code title="main.mo" %}
```javascript
public shared (msg) func apply(input : LoanApplication) : async LoanOffer {

    Debug.print("Loan Application for user " #Principal.toText(msg.caller));
        
    let offer  : LoanOffer = {
            providerid = Principal.toText(Principal.fromActor(this));
            providername = "Loan Provider";
            applicationid = 1;
            apr = 3.14;
            created = Time.now();
    };

    return offer;
};
```
{% endcode %}

To call this canister the annotated proxy Java interface [LoanProxy.java](https://github.com/ic4j/samples/blob/master/IC4JJAXBSample/src/main/org/ic4j/samples/jaxb/LoanProxy.java) and [ProxyBuilder](../proxybuilder.md) will be used.&#x20;

{% code title="LoanProxy.java" %}
```java
public interface LoanProxy {	
	@UPDATE
	@Name("apply")
	@Deserializer(JAXBDeserializer.class)
	@Waiter(timeout = 30)
	public CompletableFuture<LoanOffer> apply(@Serializer(JAXBSerializer.class) @Argument(Type.RECORD) LoanApplication loanApplication);	
}
```
{% endcode %}

LoanApplication and LoanOffer Java classes with the JAXB annotation are defined in [LoanApplication.java](https://github.com/ic4j/samples/blob/master/IC4JJAXBSample/src/main/org/ic4j/samples/jaxb/LoanApplication.java) and [LoanOffer.java](https://github.com/ic4j/samples/blob/master/IC4JJAXBSample/src/main/org/ic4j/samples/jaxb/LoanOffer.java).

{% code title="LoanApplication.java" %}
```java
@XmlRootElement(name = "data", namespace="http://ic4j.org/samples")
public class LoanApplication{
	@XmlElement(name="id", namespace="http://ic4j.org/samples", required=true)
    public BigInteger id;
	@XmlElement(name="amount", namespace="http://ic4j.org/samples", required=true)
	public Double amount;
	@XmlElement(name="term", namespace="http://ic4j.org/samples", required=true)   
    public Short term;
	@XmlElement(name="firstname", namespace="http://ic4j.org/samples", required=true)
    public String firstName;
	@XmlElement(name="lastname", namespace="http://ic4j.org/samples", required=true)
    public String lastName;
	@XmlElement(name="ssn", namespace="http://ic4j.org/samples", required=true)
    public String ssn;
	@XmlElement(name="zipcode", namespace="http://ic4j.org/samples", required=true)
    public String zipcode;
	@XmlElement(name="created", namespace="http://ic4j.org/samples", required=true)
    public BigInteger created;
}
```
{% endcode %}

{% code title="LoanOffer.java" %}
```java
@XmlRootElement(name = "data", namespace="http://ic4j.org/samples")
public class LoanOffer{
	@XmlElement(name="providerid", namespace="http://ic4j.org/samples", required=true)
    public String providerId;	
	@XmlElement(name="providername", namespace="http://ic4j.org/samples", required=true)
    public String providerName;    
	@XmlElement(name="userid", namespace="http://ic4j.org/samples", required=true)
    public Principal userId;
	@XmlElement(name="applicationid", namespace="http://ic4j.org/samples", required=true)
    public Integer applicationId;
	@XmlElement(name="apr", namespace="http://ic4j.org/samples", required=true)
    public Double apr;
	@XmlElement(name="created", namespace="http://ic4j.org/samples", required=true)
    public Long created;
}
```
{% endcode %}

The example uses the file with [XML LoanApplication payload ](https://github.com/ic4j/samples/blob/master/IC4JJAXBSample/src/resources/LoanApplication.xml)as an input.&#x20;

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

Next, create LoanApplication object using the **JAXB Unmarshaller**.&#x20;

{% code title="Main.java" %}
```java
JAXBContext context = JAXBContext.newInstance(LoanApplication.class);
LoanApplication loanApplication =  (LoanApplication) context.createUnmarshaller()		
	      .unmarshal(Main.class.getClassLoader().getResourceAsStream(LOAN_APPLICATION_FILE));
```
{% endcode %}

By default, JAXB**Serializer** and JAXB**Deserializer** will use [Java to Candid type mapping](../supported-types.md) and use the Java member variable name as the name of the Candid RECORD field.&#x20;

Use JAXB annotations [@XmlElement](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/annotation/XmlElement.html), [@XmlAttribute](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/annotation/XmlAttribute.html) or [@XmlEnumValue](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/annotation/XmlEnumValue.html) to override the default  name.

To skip serialization and deserialization of certain member variables, use the[ @XmlTransient](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/annotation/XmlTransient.html) annotation.

[ProxyBuilder](../proxybuilder.md) will  use JAXBSerializer and JAXBDeserializer defined in Java Candid [Serializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/annotations/Serializer.java) and [Deserializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/annotations/Deserializer.java) annotations to convert Candid RECORD to a Java JAXB object.

{% code title="Main.java" %}
```java
LoanOffer loanOffer = loanProxy.apply(loanApplication).get();
```
{% endcode %}

To use [Raw methods](../using-raw-methods.md) or [QueryBuilder](../querybuilder-and-updatebuilder.md#querybuilder) and [UpdateBuilder](../querybuilder-and-updatebuilder.md#updatebuilder) use JAXBSerializer and JAXBDeserializer directly in the Java code.

```java
IDLValue idlValue = IDLValue.create(loanApplication, new JAXBSerializer());
List<IDLValue> args = new ArrayList<IDLValue>();
args.add(idlValue);
IDLArgs idlArgs = IDLArgs.create(args);

byte[] payload = idlArgs.toBytes();

CompletableFuture<byte[]> response = UpdateBuilder
	.create(agent, Principal.fromString(canisterid), "apply")
	.expireAfter(Duration.ofMinutes(3))
	.arg(payload)
	.callAndWait(Waiter.create(60, 5));
	
byte[] output = queryResponse.get();

LoanOffer loanOffer = IDLArgs.fromBytes(output).getArgs().get(0).getValue(new JAXBDeserializer(), LoanOffer.class);	
```
