# ScriptRunner
Allows to virtually use `source script.sql` with your JDBC 

Initial code was duplicated from: http://pastebin.com/f10584951

Initial code was found using: http://stackoverflow.com/a/1044837/334493

The initial code didn't allow custom delimiters (at least not dynamically) so it was not suited for procedure declarations.

# Example

Here is an example of a supported script:

```sql
drop procedure if exists RecordStep;

delimiter $$

create procedure RecordStep(step varchar (50))
begin
        select 1000*(sysdate(6) - @now), 0 + sysdate(6) into @diff, @now;
        insert into bicou_gtfs.timetracker (query_id, date_added, step, duration)
        values (@queryId, now(), step, @diff);
end$$

delimiter ;

call RecordStep("test");
call RecordStep("this will work too");
```

# Usage

It is used pretty straightforwardly:

```java
try {
    Class.forName("com.mysql.jdbc.Driver");
    mConnection = DriverManager.getConnection("jdbc:mysql://" + SERVER + "/" + DATABASE + "?" + "user=" + USER + "&password=" + PASSWORD);
} catch (ClassNotFoundException e) {
    System.err.println("Unable to get mysql driver: " + e);
} catch (SQLException e) {
    System.err.println("Unable to connect to server", e);
}
ScriptRunner runner = new ScriptRunner(mConnection, false, false);
String file = "~/path/to/script.sql";
runner.runScript(new BufferedReader(new FileReader(file)));
```
