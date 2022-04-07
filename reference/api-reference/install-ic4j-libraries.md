# Install IC4J Libraries

The best way to include IC4J libraries in your Java application project is to use Gradle or Maven imports from Maven Central.

{% tabs %}
{% tab title="Gradle" %}
```
implementation 'org.ic4j:ic4j-agent:0.6.7'
implementation 'org.ic4j:ic4j-candid:0.6.6'
```
{% endtab %}

{% tab title="Maven" %}
```xml
<dependency>
  <groupId>org.ic4j</groupId>
  <artifactId>ic4j-agent</artifactId>
  <version>0.6.7</version>
</dependency>
<dependency>
  <groupId>org.ic4j</groupId>
  <artifactId>ic4j-candid</artifactId>
  <version>0.6.6</version>
</dependency>
```
{% endtab %}
{% endtabs %}

