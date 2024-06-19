# Install IC4J Libraries

The best way to include IC4J libraries in the Java application project is to use **Gradle** or **Maven Imports** from **Maven Central**.

{% tabs %}
{% tab title="Gradle" %}
```
implementation 'org.ic4j:ic4j-agent:0.7.0'
implementation 'org.ic4j:ic4j-candid:0.7.0'
```
{% endtab %}

{% tab title="Maven" %}
```xml
<dependency>
  <groupId>org.ic4j</groupId>
  <artifactId>ic4j-agent</artifactId>
  <version>0.7.0</version>
</dependency>
<dependency>
  <groupId>org.ic4j</groupId>
  <artifactId>ic4j-candid</artifactId>
  <version>0.7.0</version>
</dependency>
```
{% endtab %}
{% endtabs %}

