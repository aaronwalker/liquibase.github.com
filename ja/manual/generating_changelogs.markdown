====== 変更ログの生成 ======

LiquiBase をすでに存在するデータベースに対して使い始めるときは、現在のデータベーススキーマを作成する変更ログを作成するのが、とくにテストなどの目的でも、有効です。LiquiBase は、"generateChangeLog" [[ command line | コマンドライン ]] コマンドを利用します。

注意事項 このコマンドは現在いくつかの制限事項があり、下記の種類のオブジェクトをエクスポートしません。
  * ストアドプロシージャ、ファンクション、パッケージ
  * トリガー

===== 例 =====

<code>
liquibase --driver=oracle.jdbc.OracleDriver \
      --classpath=\path\to\classes:jdbcdriver.jar \
      --changeLogFile=com/example/db.changelog.xml \
      --url="jdbc:oracle:thin:@localhost:1521:XE" \
      --username=scott \
      --password=tiger \
      generateChangeLog
</code>

<code xml>
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog/1.1"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog/1.1
    http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-1.1.xsd">
    <changeSet author="diff-generated" id="1185214997195-1">
        <createTable name="BONUS">
            <column name="ENAME" type="VARCHAR2(10,0)"/>
            <column name="JOB" type="VARCHAR2(9,0)"/>
            <column name="SAL" type="NUMBER(22,0)"/>
            <column name="COMM" type="NUMBER(22,0)"/>
        </createTable>
    </changeSet>
    <changeSet author="diff-generated" id="1185214997195-2">
        <createTable name="DEPT">
            <column name="DEPTNO" type="NUMBER(2,0)"/>
            <column name="DNAME" type="VARCHAR2(14,0)"/>
            <column name="LOC" type="VARCHAR2(13,0)"/>
        </createTable>
    </changeSet>
    <changeSet author="diff-generated" id="1185214997195-3">
        <createTable name="EMP">
            <column name="EMPNO" type="NUMBER(4,0)"/>
            <column name="ENAME" type="VARCHAR2(10,0)"/>
            <column name="JOB" type="VARCHAR2(9,0)"/>
            <column name="MGR" type="NUMBER(4,0)"/>
            <column name="HIREDATE" type="DATE(7,0)"/>
            <column name="SAL" type="NUMBER(7,2)"/>
            <column name="COMM" type="NUMBER(7,2)"/>
            <column name="DEPTNO" type="NUMBER(2,0)"/>
        </createTable>
    </changeSet>
    <changeSet author="diff-generated" id="1185214997195-4">
        <createTable name="SALGRADE">
            <column name="GRADE" type="NUMBER(22,0)"/>
            <column name="LOSAL" type="NUMBER(22,0)"/>
            <column name="HISAL" type="NUMBER(22,0)"/>
        </createTable>
    </changeSet>
    <changeSet author="diff-generated" id="1185214997195-5">
        <addForeignKeyConstraint baseColumnNames="DEPTNO"
            baseTableName="DEPT" constraintName="FK_NAME"
            referencedColumnNames="DEPTNO" referencedTableName="EMP"/>
    </changeSet>
    <changeSet author="diff-generated" id="1185214997195-6">
        <createIndex indexName="PK_DEPT" tableName="DEPT">
            <column name="DEPTNO"/>
        </createIndex>
    </changeSet>
    <changeSet author="diff-generated" id="1185214997195-7">
        <createIndex indexName="PK_EMP" tableName="EMP">
            <column name="EMPNO"/>
        </createIndex>
    </changeSet>
    <changeSet author="diff-generated" id="1185214997195-8">
        <addPrimaryKey columnNames="DEPTNO" tableName="DEPT"/>
    </changeSet>
    <changeSet author="diff-generated" id="1185214997195-9">
        <addPrimaryKey columnNames="EMPNO" tableName="EMP"/>
    </changeSet>
</databaseChangeLog>
</code>
