# Android Development

IC4J Agent library can be also used for development of native Android application written in Java or Kotlin. Use [Android Studio](https://developer.android.com/studio) to start a new Android application or add support for the Internet Computer to your existing application.

To add required IC4J libraries to your Android project open **gradle.build** file and add dependencies:

```
    implementation 'commons-codec:commons-codec:1.17.0'
    implementation 'org.ic4j:ic4j-candid:0.7.0'
    implementation('org.ic4j:ic4j-agent:0.7.0') {
        exclude group: 'org.apache.httpcomponents.client5', module: 'httpclient5'
    }
    implementation 'org.slf4j:slf4j-api:2.0.13'
```

Android application preferably uses [OkHttp HTTP](replicatransport.md#okhttp-client-transport-implementation) client so Apache HTTP 5 library can be excluded.

To be able to connect to the Internet Computer Canister set **uses-permission** in project AndroidManifest.xml descriptor.

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

## Use IC4J with Kotlin

[AndroidHelloWord](https://github.com/ic4j/samples/tree/master/AndroidHelloWorld) sample application demonstrates use or [QueryBuilder](querybuilder-and-updatebuilder.md#querybuilder) and [UpdateBuilder](querybuilder-and-updatebuilder.md#updatebuilder) in Kotlin Android project. Project properties **ic.location** and **ic.canister** are stored in [strings.xml](https://github.com/ic4j/samples/blob/master/AndroidHelloWorld/app/src/main/res/values/strings.xml) file.

[MainActivity.kt](https://github.com/ic4j/samples/blob/master/AndroidHelloWorld/app/src/main/java/org/ic4j/samples/android/helloworld/MainActivity.kt) has a simple code for QUERY and UPDATE invocation of the Canister code [main.mo](https://github.com/ic4j/samples/blob/master/AndroidHelloWorld/main.mo).

{% code title="main.mo" %}
```javascript
actor {
    stable var name = "Me";

    public func greet(value : Text) : async Text {
        name := value;
        return "Hello, " # name # "!";
    };

    public shared query func peek() : async Text {
        return name;
    };
};
```
{% endcode %}

Using [QueryBuilder](querybuilder-and-updatebuilder.md#querybuilder) in Kotlin.

{% code title="MainActivity.kt" %}
```kotlin
val url: String = getString(R.string.url)
val canister: String = getString(R.string.canister)

val identity: Identity = AnonymousIdentity()
val transport: ReplicaTransport =
                ReplicaOkHttpTransport.create(url)

val agent: Agent = AgentBuilder().identity(identity).transport(transport).build()
val buf = IDLArgs.create(ArrayList<IDLValue>()).toBytes()
val principal = Principal.fromString(canister);

val response = QueryBuilder.create(
                        agent,
                        principal,
                        "peek"
           ).arg(buf).call()

val output = response.get()
val outArgs = IDLArgs.fromBytes(output)
name = outArgs.args[0].getValue<String>()
```
{% endcode %}

Using [UpdateBuilder](querybuilder-and-updatebuilder.md#updatebuilder) in Kotlin.

{% code title="MainActivity.kt" %}
```kotlin
val url : String = getString(R.string.url)
val canister : String = getString(R.string.canister)

val identity : Identity = AnonymousIdentity()
val transport: ReplicaTransport =
                ReplicaOkHttpTransport.create(url)
val agent: Agent = AgentBuilder().identity(identity).transport(transport).build()

val arg : IDLValue = IDLValue.create(message)
var args : ArrayList<IDLValue> = ArrayList<IDLValue>()
args.add(arg)
val buf = IDLArgs.create(args).toBytes()

val response = UpdateBuilder.create(
                        agent,
                        Principal.fromString(canister),
                        "greet"
            ).arg(buf).callAndWait(Waiter.create(60, 5))

val output = response.get()
val outArgs = IDLArgs.fromBytes(output)
val outputMessage = outArgs.args[0].getValue<String>()
```
{% endcode %}
