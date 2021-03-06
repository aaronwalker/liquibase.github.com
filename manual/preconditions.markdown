====== LiquiBase: <preConditions> ======

Preconditions can be attached to [[databasechangelog|change logs]] or [[changeset]]s to control the execution of an update based on the state of the database.

There are several reasons to use preconditions, including:
  * Document what assumptions the writers of the changelog had when creating it.
  * Enforce that those assumptions are not violated by users running the changelog
  * Perform data checks before performing an unrecoverable change such as [[drop_Table]]
  * Control what changesets are run and not run based on the state of the database


If desired, a precondition can be the only tag in a <changeSet>.





===== Sample With Preconditions =====

<code xml>
<?xml version="1.0" encoding="UTF-8"?>

<databaseChangeLog
  xmlns="http://www.liquibase.org/xml/ns/dbchangelog/1.8"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog/1.8
         http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-1.8.xsd">
    <preConditions>
        <dbms type="oracle" />
        <runningAs username="SYSTEM" />
    </preConditions>

    <changeSet id="1" author="bob">
        <preConditions onFail="WARN">
            <sqlCheck expectedResult="0">select count(*) from oldtable</sqlCheck>
        </preConditions>
        <comment>Comments should go after preCondition. If they are before then liquibase usually gives error.</comment>
        <dropTable tableName="oldtable"/>
    </changeSet>
</databaseChangeLog>
</code>                    

The above changelog will only run if the database executed against is Oracle and the database user executing the script is "SYSTEM".  It will also only run the [[drop_Table]] command if there are no values in the "oldtable".

===== Handling Failures and Errors =====

LiquiBase distinguishes between precondition "failures" (check failed) and "errors" (exception thrown in execution of check) and the reaction to both can be controlled via the "onFail" and "onError" attributes on the <preConditions> tag.  //Since 1.8//

=== Available attributes ===

^ onFail | What to do when preconditions fail (see below)  | 
^ onError | What to do when preconditions error (see below) | 
^ onUpdateSQL | What to do in updateSQL mode (see below) //Since 1.9.5// | 
^ onFailMessage | Custom message to output when preconditions fail //Since 2.0// | 
^ onErrorMessage | Custom message to output when preconditions fail //Since 2.0//  | 


=== Possible onFail/onError values ===

^ HALT | Immediately halt execution of entire change log **[default]**  | 
^ CONTINUE | Skip over change set.  Execution of change set will be attempted again on the next update.  Continue with change log.| 
^ MARK_RAN | Skip over change set, but mark it as ran.  Continue with change log| 
^ WARN | Output warning and continue executing change set as normal.  | 

Outside a changeset (e.g. at the beginning of the change log) only HALT and WARN are possible values.

=== Possible onUpdateSQL values ===

^ RUN | Run the changeSet in updateSQL mode|
^ FAIL | Fail the preCondition in updateSQL mode|
^ IGNORE | Ignore the preCondition in updateSQL mode|


===== AND/OR/NOT Logic =====

Conditional logic can be applied to preconditions using nestable <and>, <or> and <not> tags. **If no conditional tags are specified, it defaults to AND**.

Examples:

<code xml>
    <preConditions onFail="WARN">
        <dbms type="oracle" />
        <runningAs username="SYSTEM" />
    </preConditions>
</code>                    

Will check that the update is running on Oracle AND with the SYSTEM user, but will only generate a warning if the precondition fails.

<code xml>
 <preConditions>
     <dbms type="oracle" />
     <dbms type="mysql" />
 </preConditions>
</code>                 

Will require running on Oracle AND MySQL, which will always be false, unless a huge and unexpected merger takes place.

<code xml>
 <preConditions>
     <or>
         <dbms type="oracle" />
         <dbms type="mysql" />
     </or>
 </preConditions>
</code>                 

Will require running on Oracle OR MySQL which makes more sense than the above example.

<code xml>
 <preConditions>
     <or>
         <and>
            <dbms type="oracle" />
            <runningAs username="SYSTEM" />
         </and>
         <and>
            <dbms type="mssql" />
            <runningAs username="sa" />
         </and>
     </or>
 </preConditions>
</code>                 

Will require running as SYSTEM if executing against an Oracle database or running as SA if running against a MS-SQL database.


===== Available Preconditions =====


==== <dbms> ====

Passes if the database executed against matches the type specified.

=== Available Attributes ===

^ type | Type of [[../databases|database]] expected **[required]**  | 

==== <runningAs> ====

Passes if the database user executed under matches the username specified.

=== Available Attributes ===

^ username | Database user script is expected to run as **[required]**  | 

==== <changeSetExecuted> ====

Passes if the specified change set has already been executed. //Since 1.8//

=== Available Attributes ===

^ id  | Change set "id" **[required]**  | 
^ author  | Change set "author" **[required]**  | 
^ changeLogFile  | File name (including classpath relative path) of change set **[required]**  | 

==== <columnExists> ====

Passes if the specified column exists in the database. //Since 1.8//

=== Available Attributes ===

^ schemaName  | Name of the table's schema **[required]**  | 
^ tableName  | Name of the column's table **[required]**  | 
^ columnName  | Name of column **[required]**  | 

==== <tableExists> ====

Passes if the specified table exists in the database. //Since 1.8//

=== Available Attributes ===

^ schemaName  | Name of the table's schema **[required]**  | 
^ tableName  | Name of the table **[required]**  | 

==== <viewExists> ====

Passes if the specified view exists in the database. //Since 1.8//

=== Available Attributes ===

^ schemaName  | Name of the view's schema **[required]**  | 
^ viewName  | Name of the view **[required]**  | 


==== <foreignKeyConstraintExists> ====

Passes if the specified foreign key exists in the database. //Since 1.8//

=== Available Attributes ===

^ schemaName  | Name of the foreign key's schema **[required]**  | 
^ foreignKeyName  | Name of the foreign key **[required]**  | 


==== <indexExists> ====

Passes if the specified index exists in the database. //Since 1.8//

=== Available Attributes ===

^ schemaName  | Name of the index's schema **[required]**  | 
^ indexName  | Name of the index **[required]**  | 

==== <sequenceExists> ====

Passes if the specified sequence exists in the database. //Since 1.8//

=== Available Attributes ===

^ schemaName  | Name of the sequences's schema **[required]**  | 
^ sequenceName  | Name of the sequence **[required]**  | 


==== <primaryKeyExists> ====

Passes if the specified primary key exists in the database.  //Since 1.8//

=== Available Attributes ===

^ schemaName  | Name of the primary key's schema  | 
^ primaryKeyName  | Name of the primary key **[tableName OR primaryKeyName required]**  | 
^ tableName  | Name of the table containing primary key **[tableName OR primaryKeyName required]** //Since 1.9// | 

==== <sqlCheck> ====

Executes an SQL string and checks the returned value.  The SQL must return a single row with a single value.  To check numbers of rows, use the "count" SQL function.  To check for ranges of values, perform the check in the SQL and return a value that can be easily compared against.

<code xml>
<sqlCheck expectedResult="1">SELECT COUNT(1) FROM pg_tables WHERE TABLENAME = 'myRequiredTable'</sqlCheck>
</code>

=== Available Attributes ===

^ expectedResult  | Value to compare the SQL result to. **[required]**  | 

==== <changeLogPropertyDefined> ====

Checks whether given [[http://www.liquibase.org/manual/changelog_parameters#property|changelog parameter]] is present. If a value is also given, it only fails, if the value is not the same as given. //Since 2.0//

<code xml>
<changeLogPropertyDefined property="myproperty"/>
<changeLogPropertyDefined property="myproperty" value="requiredvalue"/>
</code>

=== Available Attributes ===

^ property  | Name of the property to check for. **[required]**  | 
^ value  | Required value for given property.  | 

==== <customPrecondition> ====

Custom preconditions can be created by creating a class that implements the [[http://www.liquibase.org/manual/latest/api/liquibase/precondition/CustomPrecondition.html|liquibase.precondition.CustomPrecondition]] interface.  Parameters on custom classes are set through reflection based on the <param> sub-tags.  Parameters are passed as strings to the custom precondition.

<code xml>
<customPrecondition className="com.example.CustomTableCheck">
    <param name="tableName" value="our_table"/>
    <param name="count" value="42"/>
</customPrecondition>
</code>

=== Available Attributes ===

^ className  | Name of custom precondition class. **[required]**  | 

=== Available Sub-Tags ===

^ param  | Parameter to pass to the custom precondition.  | 

== Available "param" sub-tag Attributes ==

^ name  | Name of the parameter to set. **[required]**  | 
^ value | String value to set parameter to. **[required]**  | 

===== Implementation Notes =====

Preconditions are checked at the beginning of the execution of a particular changelog. If you use the "include" tag and only have preconditions on the child changelog, those preconditions will not be checked until the migrator reaches that file. This behavior may change in future releases, so don't rely on this behavior.
