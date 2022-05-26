# Handle Binary Payloads

The Internet Computer also allows developers to send and receive binary data like JPG or PNG images.

A fully functional example of how to use IC4J API to call a Canister method with **binary payload** can be found [here](https://github.com/ic4j/samples/tree/master/IC4JImageSample).

This is an example to use Motoko to call the canister. [canister code](https://github.com/ic4j/samples/blob/master/IC4JImageSample/src/main.mo)&#x20;

The canister will receive a binary payload in **add** function and stores it. The Function **get** then returns the stored payload.

The Binary payload type is an array of Nat8.

{% code title="main.mo" %}
```javascript
actor {
  let images = Map.HashMap<Text, Blob>(0, Text.equal, Text.hash);
  
  public func add(name : Text, image : [Nat8]) : async Text {
    let blob : Blob = Blob.fromArray(image);
    Debug.print("Source Image Size " #debug_show(blob.size()));
    images.put(name, blob );
    return  name;
  };

  public query func get(name : Text) : async [Nat8] {
    let blob : ?Blob = images.get(name);
    switch blob {
            case (null) { return [] };
            case (?image) { 
              Debug.print("Result Image Size " #debug_show(image.size()));
              Blob.toArray(image);
               };
        };  
  };
};
```
{% endcode %}

In Java the [proxy interface](proxybuilder.md) [ImageProxy](https://github.com/ic4j/samples/blob/master/IC4JImageSample/src/main/java/org/ic4j/samples/image/ImagesProxy.java) is created with the 2 methods **get** and **add**.

{% code title="ImagesProxy.java" %}
```java
public interface ImagesProxy {	
	@QUERY
	@Name("get")
	public byte[] get(@Argument(Type.TEXT)String name );	
	
	@UPDATE
	@Name("add")
	@Waiter(timeout = 30)
	public CompletableFuture<String> add(@Argument(Type.TEXT)String name, @Argument(Type.NAT8)byte[] image);
}
```
{% endcode %}

Then in a simple Java class , [ProxyBuilder](proxybuilder.md) can be used to create Canister Java proxy.&#x20;

The source can be found in M[ain.java](https://github.com/ic4j/samples/blob/master/IC4JImageSample/src/main/java/org/ic4j/samples/image/Main.java) file.

{% code title="Main.java" %}
```java
byte[] image = getImage(IMAGE_FILE, "png");
		
String name  = IMAGE_FILE;
ImagesProxy images = ProxyBuilder.create(agent, Principal.fromString(icCanister))
				.getProxy(ImagesProxy.class);		
CompletableFuture<String> proxyResponse = images.add(name, image);

String output = proxyResponse.get();		
byte[] imageResult = images.get(name);	
```
{% endcode %}

Binary Candid payload (\[Nat8]) can be represented in Java either as byte\[] array or Byte\[] array.
