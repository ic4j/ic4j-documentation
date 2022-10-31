# Pojo Serializer and Deserializer

Use [PojoSerializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/pojo/PojoSerializer.java) and [PojoDeserializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/pojo/PojoDeserializer.java) to serialize and deserialize the annotated POJO (Plain Old Java Object) to and from Candid payload of type RECORD.&#x20;

Serializer and Deserializer will use Candid Java annotations to get the Candid Name and Type.&#x20;

A fully functional example using [PojoSerializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/pojo/PojoSerializer.java) and [PojoDeserializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/pojo/PojoDeserializer.java) can be found [here](https://github.com/ic4j/samples/tree/master/IC4JPojoSample).

This example calls a canister using Motoko [main.mo](https://github.com/ic4j/samples/blob/master/IC4JPojoSample/src/main.mo).&#x20;

The canister uses 2 complex types, **LoanApplication** and **LoanOffer.**

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

The canister has 2 methods **apply** and **getOffers.**

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

To call this canister the annotated proxy Java interface [LoanProxy.java](https://github.com/ic4j/samples/blob/master/IC4JPojoSample/src/main/java/org/ic4j/samples/pojo/LoanProxy.java) and [ProxyBuilder](../proxybuilder.md) will be used.&#x20;

{% code title="LoanProxy.java" %}
```java
public interface LoanProxy {
	@UPDATE
	@Name("apply")
	@Waiter(timeout = 30)
	@ResponseClass(LoanOffer.class)
	public CompletableFuture<LoanOffer> apply(@Argument(Type.RECORD) LoanApplication loanApplication);
	
	@QUERY
	@Name("getOffers")
	public LoanOffer[] getOffers();
}
```
{% endcode %}

LoanApplication and LoanOffer Java classes with the Candid annotation are defined in [LoanApplication.java](https://github.com/ic4j/samples/blob/master/IC4JPojoSample/src/main/java/org/ic4j/samples/pojo/LoanApplication.java) and [LoanOffer.java](https://github.com/ic4j/samples/blob/master/IC4JPojoSample/src/main/java/org/ic4j/samples/pojo/LoanOffer.java).

{% code title="LoanApplication.java" %}
```java
public class LoanApplication{
    @Field(Type.NAT)
    public BigInteger id;
    public Double amount;
    @Field(Type.NAT16)    
    public Short term;
    @Name("firstname")
    public String firstName;
    @Name("lastname")
    public String lastName;
    public String ssn;
    public String zipcode;
    public BigInteger created;
}
```
{% endcode %}

{% code title="LoanOffer.java" %}
```java
public class LoanOffer{
    @Field(Type.PRINCIPAL)
    @Name("providerid")
    public String providerId;	
    @Name("providername")
    public String providerName;    
    @Field(Type.PRINCIPAL)
    @Name("userid")
    public Principal userId;
    @Field(Type.NAT)
    @Name("applicationid")
    public Integer applicationId;
    public Double apr;
    @Field(Type.INT)
    public Long created;
}
```
{% endcode %}

By default, **PojoSerializer** and **PojoDeserializer** will use [Java to Candid type mapping](../supported-types.md) and use the Java member variable name as the name of the Candid RECORD field.&#x20;

Use Candid annotations [@Field](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/annotations/Field.java) to override the default type and [@Name](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/annotations/Name.java) to override the default name.

To skip serialization and deserialization of certain member variables, use the [@Ignore](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/annotations/Ignore.java) annotation.

[ProxyBuilder](../proxybuilder.md) will implicitly use PojoSerializer and PojoDeserializer to convert Candid RECORD to a Java object.

{% code title="Main.java" %}
```java
LoanApplication loanApplication = new LoanApplication();
loanApplication.firstName = "John";
loanApplication.lastName = "Doe";
loanApplication.ssn = "111-11-1111";
loanApplication.term = 48;
loanApplication.zipcode = "95134";		
loanApplication.amount = (double) 20000.00;
loanApplication.id = new BigInteger("11");
loanApplication.created = new BigInteger("0");
		
LoanProxy loanProxy = ProxyBuilder
		.create(agent, Principal.fromString(icCanister))
		.getProxy(LoanProxy.class);
		
CompletableFuture<LoanOffer> response = loanProxy.apply(loanApplication);		
LoanOffer loanOffer = response.get();				
LOG.info("Loan Offer APR is " + loanOffer.apr);		
```
{% endcode %}

To use [Raw methods](../using-raw-methods.md) or [QueryBuilder](../querybuilder-and-updatebuilder.md#querybuilder) and [UpdateBuilder](../querybuilder-and-updatebuilder.md#updatebuilder) use PojoSerializer and PojoDeserializer directly in the Java code.

{% code title="Main.java" %}
```java
IDLValue idlValue = IDLValue.create(loanApplication, new PojoSerializer());
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

LoanOffer loanOffer = IDLArgs.fromBytes(output).getArgs().get(0).getValue(new PojoDeserializer(), LoanOffer.class);	
```
{% endcode %}
