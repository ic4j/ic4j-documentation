# Object Serializers and Deserializers

To handle complex Candid types RECORD and VARIANT IC4J use custom [ObjectSerializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/ObjectSerializer.java) and [ObjectDeserializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/ObjectDeserializer.java) implementations.&#x20;

If required, developers can design their own serializers and deserializers.&#x20;

The IC4J library comes with several implementations of the most common scenarios (Java Pojo, JSON, XML, JDBC).

Use Pojo Serializer and Deserializer to handle plain Java objects.

{% content-ref url="pojo-serializer-and-deserializer.md" %}
[pojo-serializer-and-deserializer.md](pojo-serializer-and-deserializer.md)
{% endcontent-ref %}

Use JSON Jackson Serializer and Deserializer to handle Jackson JSON objects.

{% content-ref url="json-jackson-serializer-and-deserializer.md" %}
[json-jackson-serializer-and-deserializer.md](json-jackson-serializer-and-deserializer.md)
{% endcontent-ref %}

Use JSON Gson Serializer and Deserializer to handle Gson JSON objects.

{% content-ref url="json-gson-serializer-and-deserializer.md" %}
[json-gson-serializer-and-deserializer.md](json-gson-serializer-and-deserializer.md)
{% endcontent-ref %}

Use XML Serializer and Deserializer to handle XML DOM objects.

{% content-ref url="xml-serializer-and-deserializer.md" %}
[xml-serializer-and-deserializer.md](xml-serializer-and-deserializer.md)
{% endcontent-ref %}

Use JDBC Serializer to handle JDBC objects.

{% content-ref url="jdbc-serializer.md" %}
[jdbc-serializer.md](jdbc-serializer.md)
{% endcontent-ref %}
