# JSON Gson Serializer and Deserializer

Alternative option to work with JSON in Java is to use [Google Gson](https://github.com/google/gson) open source library.

To use [IC4J Candid Gson library](https://github.com/ic4j/ic4j-candid-gson) add dependencies to Gradle or Maven project.

{% tabs %}
{% tab title="Gradle" %}
```
implementation 'org.ic4j:ic4j-candid-gson:0.6.19'
```
{% endtab %}

{% tab title="Maven" %}
```xml
<dependency>
  <groupId>org.ic4j</groupId>
  <artifactId>ic4j-candid-gson</artifactId>
  <version>0.6.19</version>
</dependency>
```
{% endtab %}
{% endtabs %}

Use [GsonSerializer](https://github.com/ic4j/ic4j-candid-gson/blob/master/src/main/java/org/ic4j/candid/gson/GsonSerializer.java) and [GsonDeserializer](https://github.com/ic4j/ic4j-candid-gson/blob/master/src/main/java/org/ic4j/candid/gson/GsonDeserializer.java) to serialize and deserialize [Java Gson](https://github.com/google/gson) JSON object to and from the Candid payload of type RECORD.&#x20;

A fully functional example using GsonSerializer and GsonDeserializer can be found [here](https://github.com/ic4j/samples/tree/master/IC4JGsonSample).

This example uses Motoko to call the canister [main.mo](https://github.com/ic4j/samples/blob/master/IC4JGsonSample/src/main.mo). The canister uses 2 complex types, **LoanApplication** and **LoanOffer.**

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

The example uses the file with JSON [LoanApplication payload](https://github.com/ic4j/samples/blob/master/IC4JGsonSample/src/resources/LoanApplication.json) as an input.&#x20;

{% code title=" LoanApplication.json" %}
```json
{
"id" : 0,
"firstname" : "John",
"lastname" : "Doe",
"zipcode" : "99999",
"ssn" : "111-11-1111",
"amount" : 2000.00,
"term" : 24,
"created" : 0
}
```
{% endcode %}

To be able to properly map JSON names and values to Candid name types declare the [IDLType](../use-idlargs.md#idltype) structure as follows:

{% code title="Main.java" %}
```java
Map<Label,IDLType> applicationRecord = new TreeMap<Label,IDLType>();
applicationRecord.put(Label.createNamedLabel("id"), IDLType.createType(Type.NAT));
applicationRecord.put(Label.createNamedLabel("firstname"), IDLType.createType(Type.TEXT));
applicationRecord.put(Label.createNamedLabel("lastname"), IDLType.createType(Type.TEXT));
applicationRecord.put(Label.createNamedLabel("zipcode"), IDLType.createType(Type.TEXT));
applicationRecord.put(Label.createNamedLabel("ssn"), IDLType.createType(Type.TEXT));		
applicationRecord.put(Label.createNamedLabel("amount"), IDLType.createType(Type.FLOAT64));
applicationRecord.put(Label.createNamedLabel("term"), IDLType.createType(Type.NAT16));
applicationRecord.put(Label.createNamedLabel("created"), IDLType.createType(Type.INT));
		
IDLType idlType =  IDLType.createType(Type.RECORD, applicationRecord);
		
Map<Label,IDLType> offerRecord = new TreeMap<Label,IDLType>();
offerRecord.put(Label.createNamedLabel("providerid"), IDLType.createType(Type.PRINCIPAL));
offerRecord.put(Label.createNamedLabel("providername"), IDLType.createType(Type.TEXT));
offerRecord.put(Label.createNamedLabel("applicationid"), IDLType.createType(Type.NAT));	
offerRecord.put(Label.createNamedLabel("apr"), IDLType.createType(Type.FLOAT64));		
offerRecord.put(Label.createNamedLabel("created"), IDLType.createType(Type.INT));
		
IDLType resultIdlType =  IDLType.createType(Type.RECORD, offerRecord);	
```
{% endcode %}

Next, create IDLValue using the **GsonSerializer create** method.&#x20;

The Serializer input is the variable type [JsonElemen](https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5/com/google/gson/JsonElement.html)t.

{% code title="Main.java" %}
```java
JsonElement jsonValue = readNode(LOAN_APPLICATION_FILE)		
IDLValue idlValue = IDLValue.create(jsonValue, GsonSerializer.create(idlType));
		
List<IDLValue> idlArgs = new ArrayList<IDLValue>();		
idlArgs.add(idlValue);
byte[] buf = IDLArgs.create(idlArgs).toBytes();
```
{% endcode %}

Use[ UpdateBuilder](../querybuilder-and-updatebuilder.md#updatebuilder), [QueryBuilder](../querybuilder-and-updatebuilder.md#querybuilder) or[ Raw Methods](../using-raw-methods.md) to call the Canister and deserialize output to [JsonElement](https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5/com/google/gson/JsonElement.html).&#x20;

{% code title="Main.java" %}
```java
CompletableFuture<byte[]> response = UpdateBuilder.create(agent,Principal.fromString(icCanister), "apply").arg(buf).callAndWait(Waiter.create(60, 5));
		
byte[] output = response.get();
JsonElement jsonResult = IDLArgs.fromBytes(output).getArgs().get(0)
		.getValue(GsonDeserializer.create(resultIdlType), JsonElement.class);

```
{% endcode %}
