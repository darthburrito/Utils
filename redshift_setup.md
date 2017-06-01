Revoke all PRIVILEGES for all unspecified users on a database
```sql
BEGIN;
REVOKE ALL PRIVILEGES ON DATABASE dynamic FROM public;
REVOKE ALL ON schema public FROM public
END;
```

Create a schema for dynamic pricing
```sql
create schema dynamic;
```
Creating a read only group for a schema (dynamic)
```sql
BEGIN;
CREATE GROUP read_only_dynamic;
GRANT SELECT ON ALL TABLES IN SCHEMA dynamic to group read_only_dynamic;
GRANT USAGE ON  SCHEMA dynamic to group read_only_dynamic;
END;

```
Creating a read write group with no ability to create or alter tables for in schema(dynamic)
```sql
BEGIN;
CREATE GROUP read_write_dynamic;
GRANT SELECT ON ALL TABLES IN SCHEMA dynamic to group read_only_dynamic;
GRANT USAGE ON  SCHEMA dynamic to group read_only_dynamic;
GRANT INSERT ON ALL TABLES IN SCHEMA dynamic to group read_write_dynamic;
GRANT UPDATE ON ALL TABLES IN SCHEMA dynamic to group read_write_dynamic;
GRANT DELETE ON ALL TABLES IN SCHEMA dynamic to group read_write_dynamic;
END;
```
CREATE GROUP create_tables_dynamic;
```sql
BEGIN;
CREATE GROUP create_tables_dynamic;
GRANT USAGE ON  SCHEMA dynamic to group read_only_dynamic;
GRANT ALL ON ALL TABLES IN SCHEMA dynamic to group read_write_dynamic;
GRANT CREATE ON  SCHEMA dynamic to group create_tables_dynamic;
END;
```

Creating a read only user
```sql

BEGIN;
-- create user
CREATE USER read_only_user WITH password 'ReadOnly1';
-- add to group(s)
ALTER
GROUP read_only_dynamic ADD USER read_only_user;

```

Creating a read write user
```sql

BEGIN;
-- create user
CREATE USER read_write_user WITH password 'ReadOnly1';
-- add to group(s)
ALTER
GROUP read_write_dynamic ADD USER read_write_user;

END;

```
Creating a read write user
```sql

BEGIN;
-- create user
CREATE USER create_table_user WITH password 'ReadOnly1';
-- add to group(s)
ALTER
GROUP create_tables_dynamic ADD USER create_table_user;
-- alter PRIVILEGES so new objects created by this user can be read and writen by other groups
ALTER DEFAULT PRIVILEGES
FOR USER create_table_user IN SCHEMA DYNAMIC GRANT
SELECT ON TABLES TO
GROUP read_only_dynamic;

ALTER DEFAULT PRIVILEGES
FOR USER create_table_user IN SCHEMA DYNAMIC GRANT ALL ON TABLES TO
GROUP read_write_dynamic;

ALTER DEFAULT PRIVILEGES
FOR USER create_table_user IN SCHEMA DYNAMIC GRANT ALL ON TABLES TO
GROUP create_tables_dynamic;
END;

```
