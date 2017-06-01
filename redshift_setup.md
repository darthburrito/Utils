Revoke all PRIVILEGES for all unspecified users on a database
```sql
BEGIN;

REVOKE ALL PRIVILEGES ON DATABASE mydatabase
FROM PUBLIC;

REVOKE ALL ON SCHEMA PUBLIC
FROM PUBLIC END;
```

Create a schema
```sql
CREATE SCHEMA myschema;
```
Creating a read only group for a schema (myschema)
```sql
BEGIN;


CREATE
GROUP read_only_myschema;

GRANT
SELECT ON ALL TABLES IN SCHEMA myschema TO
GROUP read_only_myschema;

GRANT USAGE ON SCHEMA myschema TO
GROUP read_only_myschema;

END;
```
Creating a read write group with no ability to create or alter tables for in schema(myschema)
```sql
BEGIN;


CREATE
GROUP read_write_myschema;

GRANT
SELECT ON ALL TABLES IN SCHEMA myschema TO
GROUP read_only_myschema;

GRANT USAGE ON SCHEMA myschema TO
GROUP read_only_myschema;

GRANT
INSERT ON ALL TABLES IN SCHEMA myschema TO
GROUP read_write_myschema;

GRANT
UPDATE ON ALL TABLES IN SCHEMA myschema TO
GROUP read_write_myschema;

GRANT
DELETE ON ALL TABLES IN SCHEMA myschema TO
GROUP read_write_myschema;

END;
```
CREATE GROUP create_tables_myschema;
```sql
BEGIN;


CREATE
GROUP create_tables_myschema;

GRANT USAGE ON SCHEMA myschema TO
GROUP read_only_myschema;

GRANT ALL ON ALL TABLES IN SCHEMA myschema TO
GROUP read_write_myschema;

GRANT
CREATE ON SCHEMA myschema TO
GROUP create_tables_myschema;

END;
```

Creating a read only user
```sql
BEGIN;

-- create user

CREATE USER read_only_user WITH password 'ReadOnly1';

-- add to group(s)

ALTER
GROUP read_only_myschema ADD USER read_only_user;
```

Creating a read write user
```sql
BEGIN;

-- create user

CREATE USER read_write_user WITH password 'ReadOnly1';

-- add to group(s)

ALTER
GROUP read_write_myschema ADD USER read_write_user;

END;
```
Creating a read write user
```sql
BEGIN;

-- create user

CREATE USER create_table_user WITH password 'ReadOnly1';

-- add to group(s)

ALTER
GROUP create_tables_myschema ADD USER create_table_user;

-- alter PRIVILEGES so new objects created by this user can be read and writen by other groups

ALTER DEFAULT PRIVILEGES
FOR USER create_table_user IN SCHEMA myschema GRANT
SELECT ON TABLES TO
GROUP read_only_myschema;


ALTER DEFAULT PRIVILEGES
FOR USER create_table_user IN SCHEMA myschema GRANT ALL ON TABLES TO
GROUP read_write_myschema;


ALTER DEFAULT PRIVILEGES
FOR USER create_table_user IN SCHEMA myschema GRANT ALL ON TABLES TO
GROUP create_tables_myschema;

END;
```


References
---------
http://docs.aws.amazon.com/redshift/latest/dg/t_user_group_examples.html
http://docs.aws.amazon.com/redshift/latest/dg/r_GRANT.html
http://docs.aws.amazon.com/redshift/latest/dg/r_REVOKE.html
