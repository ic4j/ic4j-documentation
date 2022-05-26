# JSON Jackson Serializer and Deserializer

Use [JacksonSerializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/jackson/JacksonSerializer.java) and [JacksonDeserializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/jackson/JacksonDeserializer.java) to serialize and deserialize [Java Jackson](https://github.com/FasterXML/jackson) JSON object to and from the Candid payload of type RECORD.&#x20;

A fully functional example using JacksonSerializer and JacksonDeserializer can be found [here](https://github.com/ic4j/samples/tree/master/IC4JJacksonSample).

This example uses Motoko to call the canister [main.mo](https://github.com/ic4j/samples/blob/master/IC4JJacksonSample/src/main.mo). The canister uses 2 complex types, **LoanApplication** and **LoanOffer.**

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

The example uses the file with JSON [LoanApplication payload](https://github.com/ic4j/samples/blob/master/IC4JJacksonSample/src/resources/LoanApplication.json) as an input.&#x20;

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

To be able to properly map JSON names and values to Candid name types declare the [IDLType](../using-idlargs.md#idltype) structure as follows:

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

Next, create IDLValue using the **JacksonSerializer create** method.&#x20;

The Serializer input is the variable type [JsonNode](https://fasterxml.github.io/jackson-databind/javadoc/2.7/com/fasterxml/jackson/databind/JsonNode.html).

{% code title="Main.java" %}
```java
JsonNode jsonValue = readNode(LOAN_APPLICATION_FILE);
		
IDLValue idlValue = IDLValue.create(jsonValue, JacksonSerializer.create(idlType));
List<IDLValue> idlArgs = new ArrayList<IDLValue>();
idlArgs.add(idlValue);

byte[] buf = IDLArgs.create(idlArgs).toBytes();
```
{% endcode %}

Use[ UpdateBuilder](../querybuilder-and-updatebuilder.md#updatebuilder), [QueryBuilder](../querybuilder-and-updatebuilder.md#querybuilder) or[ Raw Methods](../using-raw-agent-methods.md) to call the Canister and deserialize output to [JsonNode](https://fasterxml.github.io/jackson-databind/javadoc/2.7/com/fasterxml/jackson/databind/JsonNode.html).&#x20;

{% code title="Main.java" %}
```java
CompletableFuture<byte[]> response = UpdateBuilder.create(agent,Principal.fromString(icCanister), "apply").arg(buf).callAndWait(Waiter.create(60, 5));
		
byte[] output = response.get();
JsonNode jsonResult = IDLArgs.fromBytes(output).getArgs().get(0)
			.getValue(JacksonDeserializer.create(resultIdlType), JsonNode.class);
```
{% endcode %}

