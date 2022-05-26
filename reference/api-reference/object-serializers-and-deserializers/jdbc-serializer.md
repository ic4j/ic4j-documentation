# JDBC Serializer

Use [JDBCSerializer](https://github.com/ic4j/ic4j-candid/blob/master/src/main/java/org/ic4j/candid/jdbc/JDBCSerializer.java) to serialize Java [JDBC ResultSet](https://docs.oracle.com/javase/8/docs/api/java/sql/ResultSet.html) object to the Candid payload of type RECORD.&#x20;

A fully functional example using JDBCSerializer  can be found [here](https://github.com/ic4j/samples/tree/master/IC4JJDBCSample).

This example uses Motoko to call the canister [main.mo](https://github.com/ic4j/samples/blob/master/IC4JJDBCSample/src/main.mo). The canister uses  complex type  **CreditCheck.**

{% code title="main.mo" %}
```javascript
  // Credit Check
  public type Credit = {
    ssn: Text;   
    rating: Int32;
  };
```
{% endcode %}

The canister has 1 method:  **apply** and **setCredit.**

{% code title="main.mo" %}
```javascript
    public shared (msg) func setCredit(input : Credit){
        Debug.print("Credit Check for ssn " #input.ssn);
    };
```
{% endcode %}

The example uses the data from embedded [Apache Derby](https://db.apache.org/derby/) SQL database as an input. Database table is reinitialized every time the sample runs.

{% code title="Main.java" %}
```java
Statement statement = connection.createStatement();
String sql = "CREATE TABLE data (ssn VARCHAR(11) PRIMARY KEY,rating INT)";
statement.execute(sql);
sql = "INSERT INTO data VALUES ('111-11-1111',550)";
statement.execute(sql);
sql = "INSERT INTO data VALUES ('222-22-2222',650)";
statement.execute(sql);
sql = "INSERT INTO data VALUES ('333-33-3333',750)";
statement.execute(sql);
```
{% endcode %}

Execute [JDBC PreparedStatement](https://docs.oracle.com/javase/8/docs/api/java/sql/PreparedStatement.html) to get ResultSet data from the database.

{% code title="Main.java" %}
```java
PreparedStatement statement = connection.prepareStatement("SELECT ssn, rating FROM data WHERE ssn = ?");

String ssn = "222-22-2222";
statement.setString(1, ssn);
ResultSet result = statement.executeQuery();	
```
{% endcode %}

The Serializer input is the variable of type [ResultSet](https://docs.oracle.com/javase/8/docs/api/java/sql/ResultSet.html). To serialize individual row from ResultSet to Candid RECORD use function **array** with **false** value. Otherwise the result will be Candid VEC type, wrapping Candid RECORD items.

{% code title="Main.java" %}
```java
IDLValue idlValue = IDLValue.create(result, JDBCSerializer.create().array(false));
List<IDLValue> idlArgs = new ArrayList<IDLValue>();
idlArgs.add(idlValue);

byte[] buf = IDLArgs.create(idlArgs).toBytes();
```
{% endcode %}

Use[ UpdateBuilder](../querybuilder-and-updatebuilder.md#updatebuilder), [QueryBuilder](../querybuilder-and-updatebuilder.md#querybuilder) or[ Raw Methods](../using-raw-agent-methods.md) to call the Canister.&#x20;

{% code title="Main.java" %}
```java
CompletableFuture<byte[]> response = UpdateBuilder.create(agent, Principal.fromString(icCanister), "setCredit").arg(buf)
    .callAndWait(Waiter.create(60, 5));

byte[] output = response.get();
```
{% endcode %}
