👤 Role Creation in PostgreSQL
```
What is a Role?
A role in PostgreSQL is a database-level identity that can represent a user, a group, or both. Roles can own objects, log in, and be granted privileges

🛠️ Creating a Role
CREATE ROLE myuser;

This creates a role without login capability. To allow login, use:
CREATE ROLE myuser WITH LOGIN PASSWORD 'securepassword';

🔐 Role Attributes
------------------
Attribute      Description
---------      ---------------------------------------------------------
LOGIN          Allows the role to connect to the database
SUPERUSER      Grants all privileges (use with caution!)
CREATEDB       Allows role to create new databases
CREATEROLE     Allows role to create other roles
INHERIT        Role inherits privileges from roles it is a member of
REPLICATION    Allows replication connections
BYPASSRLS      Bypasses row-level security policies  

Example with multiple attributes:
CREATE ROLE admin WITH LOGIN CREATEDB CREATEROLE INHERIT PASSWORD 'adminpass';

-Roles can be members of other roles (like groups):
GRANT dev_team TO alice;

🔄 Role Management Summary
--------------------------
Task                    Command
----                    -----------------------------------------------
Create role             CREATE ROLE myuser;
Allow login             CREATE ROLE myuser WITH LOGIN;
Set password            ALTER ROLE myuser WITH PASSWORD 'pass';
Grant role membership   GRANT roleA TO roleB;
Drop role               DROP ROLE myuser;

```

## Privileges
```
- When an objects are created in postgresql, It is assigned an owner. The owner is normally the role that ececuted the creation statement.
- Only owner and super user can do aything with the objects. To allow other roles to use objects. Privileges must be granted.

* Postgresql provides different kind of privileges 
    [SELECT,INSERT,UPDATE,DELETE,TRUNCATE,REFERANCE,USAGE,TRIGER,CREATE,CONNECT,TEMPORARY,EXECUTE]
   -These privileges are depends on objects type [table, index,sequence, functin etc..]
   - We can assign new new owner to object by using "ALTER objectname" command but this can be done by SUPERUSER and if the role is member of ownning role and current owner.

* We can Grant following three Database Level Privileges to role:-  
 -CONNECT	: Allows connection to the database
 -CREATE	: Allows creation of new schemas within the database
 -TEMPORARY : or TEMP	Allows creation of temporary tables

( GRANT CONNECT, CREATE, TEMP ON DATABASE mydb TO myuser; )

* We can Grant following 7 Tables & Views Level Privileges to role:- 
SELECT	       Read rows
INSERT	       Add rows
UPDATE	       Modify rows
DELETE	       Remove rows
TRUNCATE	     Remove all rows
REFERENCES	   Create foreign keys
TRIGGER	      Create triggers

(GRANT SELECT, INSERT, UPDATE ON TABLE mytable TO myuser;)

* We can Grant following 3 Seaquence Level Privileges to role:-
USAGE	    Use the sequence
SELECT	   Read current value
UPDATE	    Modify current value

(GRANT USAGE, SELECT ON SEQUENCE myseq TO myuser;)

* We can Grant following 1 Functions & Procedures Level Privileges to role:-
EXECUTE  :	Run the function/procedure

(GRANT EXECUTE ON FUNCTION myfunc(int) TO myuser;)

* We can grant following 2 Schemas level privileges to role:-
USAGE:	Access objects in the schema
CREATE:	Create new objects in the schema

(GRANT USAGE, CREATE ON SCHEMA myschema TO myuser;)

✅ Types of Objects You Can Grant Privileges On

Object Type           Privileges You Can Grant                          Notes
-----------------------------------------------------------------------------------------------
Database              CONNECT, CREATE, TEMP                             Controls access to the database itself
Schema                USAGE, CREATE                                     Controls access to objects inside the schema
Table                 SELECT, INSERT, UPDATE, DELETE, TRUNCATE,         Most commonly used
                      REFERENCES, TRIGGER
Sequence              USAGE, SELECT, UPDATE                             For auto-incrementing values
Function              EXECUTE                                           Needed to run stored procedures
Type                  USAGE                                             For custom data types
Foreign Data Wrapper  USAGE                                             For external data sources
Foreign Server        USAGE                                             For connecting to external servers
Large Object          SELECT, UPDATE                                    Used for binary data (less common)
Tablespace            CREATE                                            Controls where objects are stored
Column                SELECT, UPDATE, INSERT, REFERENCES                Can be granted at column level too


```
## 🧬  Default Privileges
```
-These control what privileges are automatically granted on newly created objects.
-if you configure default privileges for a role, then any new table or sequence created by that role will automatically grant those privileges to the specified target role or user.

* Use ALTER DEFAULT PRIVILEGES to set them:
ALTER DEFAULT PRIVILEGES IN SCHEMA myschema
GRANT SELECT, INSERT ON TABLES TO myuser;

* You can set defaults for:
Tables
Sequences
Functions
Types
Schemas
```

## 🛡️ Granting All Privileges
```
If you want to grant all possible privileges, use:
🔹 On a database:
GRANT ALL PRIVILEGES ON DATABASE mydb TO myuser;
🔹 On a table:
GRANT ALL PRIVILEGES ON TABLE mytable TO myuser;
🔹 On a schema:
GRANT ALL PRIVILEGES ON SCHEMA myschema TO myuser;

🧹 Bonus: Revoking Privileges
To remove access:
REVOKE ALL PRIVILEGES ON DATABASE mydb FROM 

```
## Quries for get granted priviles on objects , DB, schema level :-
```
-- 🔐 DATABASE LEVEL PRIVILEGES
SELECT 
  datname AS database_name,
  pg_catalog.pg_get_userbyid(datdba) AS owner,
  has_database_privilege('your_role', datname, 'CONNECT') AS can_connect,
  has_database_privilege('your_role', datname, 'CREATE') AS can_create,
  has_database_privilege('your_role', datname, 'TEMP') AS can_temp
FROM pg_database;

-- 🏛️ SCHEMA LEVEL PRIVILEGES
SELECT 
  n.nspname AS schema_name,
  pg_catalog.pg_get_userbyid(n.nspowner) AS owner,
  has_schema_privilege('your_role', n.nspname, 'USAGE') AS can_use,
  has_schema_privilege('your_role', n.nspname, 'CREATE') AS can_create
FROM pg_namespace n
WHERE n.nspname NOT LIKE 'pg_%' AND n.nspname <> 'information_schema';

-- 📊 TABLE & VIEW PRIVILEGES
SELECT 
  table_schema,
  table_name,
  privilege_type
FROM information_schema.role_table_grants
WHERE grantee = 'your_role';

-- 📌 COLUMN LEVEL PRIVILEGES
SELECT 
  table_schema,
  table_name,
  column_name,
  privilege_type
FROM information_schema.column_privileges
WHERE grantee = 'your_role';

-- 🔁 SEQUENCE PRIVILEGES
SELECT 
  sequence_schema,
  sequence_name,
  privilege_type
FROM information_schema.role_usage_grants
WHERE grantee = 'your_role';

-- 🧠 FUNCTION & PROCEDURE PRIVILEGES
SELECT 
  routine_schema,
  routine_name,
  privilege_type
FROM information_schema.role_routine_grants
WHERE grantee = 'your_role';


------------------------------------------------

-- 🗃️ DATABASE PRIVILEGES
SELECT datname AS database_name,
       pg_catalog.pg_get_userbyid(datdba) AS owner,
       has_database_privilege('your_role', datname, 'CONNECT') AS can_connect,
       has_database_privilege('your_role', datname, 'CREATE') AS can_create,
       has_database_privilege('your_role', datname, 'TEMP') AS can_temp
FROM pg_database;

-- 🏛️ SCHEMA PRIVILEGES
SELECT n.nspname AS schema_name,
       pg_catalog.pg_get_userbyid(n.nspowner) AS owner,
       has_schema_privilege('your_role', n.nspname, 'USAGE') AS can_use,
       has_schema_privilege('your_role', n.nspname, 'CREATE') AS can_create
FROM pg_namespace n
WHERE n.nspname NOT LIKE 'pg_%' AND n.nspname <> 'information_schema';

-- 📦 TABLE & VIEW PRIVILEGES
SELECT table_schema,
       table_name,
       privilege_type,
       grantee
FROM information_schema.role_table_grants
WHERE grantee = 'your_role';

-- 📊 COLUMN PRIVILEGES
SELECT table_schema,
       table_name,
       column_name,
       privilege_type,
       grantee
FROM information_schema.column_privileges
WHERE grantee = 'your_role';

-- 🔁 SEQUENCE PRIVILEGES
SELECT sequence_schema,
       sequence_name,
       privilege_type,
       grantee
FROM information_schema.role_usage_grants
WHERE grantee = 'your_role';

-- 🧠 FUNCTION & PROCEDURE PRIVILEGES
SELECT routine_schema,
       routine_name,
       specific_name,
       privilege_type,
       grantee
FROM information_schema.role_routine_grants
WHERE grantee = 'your_role';

-- 🧱 TABLESPACE PRIVILEGES
SELECT spcname AS tablespace_name,
       pg_catalog.pg_get_userbyid(spcowner) AS owner,
       has_tablespace_privilege('your_role', spcname, 'CREATE') AS can_create
FROM pg_tablespace;

-- 🧊 LARGE OBJECT PRIVILEGES
SELECT loid AS large_object_oid,
       pg_catalog.array_to_string(lomacl, ',') AS privileges
FROM pg_largeobject_metadata
WHERE lomacl IS NOT NULL AND
      EXISTS (
        SELECT 1 FROM pg_roles WHERE rolname = 'your_role'
        AND POSITION(rolname IN pg_catalog.array_to_string(lomacl, ',')) > 0
      );

-- 🌐 FOREIGN DATA WRAPPER PRIVILEGES
SELECT fdwname AS foreign_data_wrapper,
       has_foreign_data_wrapper_privilege('your_role', fdwname, 'USAGE') AS can_use
FROM pg_foreign_data_wrapper;

-- 🌍 FOREIGN SERVER PRIVILEGES
SELECT srvname AS foreign_server,
       has_server_privilege('your_role', srvname, 'USAGE') AS can_use
FROM pg_foreign_server;

```




















# psql-meta-cmd

```

+----------------------+------------------+-----------------------------+--------------------------------------------------------------+
| Command              | Category         | Purpose                     | Usage & When to Use                                          |
+----------------------+------------------+-----------------------------+--------------------------------------------------------------+
| \?                   | Help             | Show meta-command help      | Use when unsure about available commands or syntax.          |
| \h                   | Help             | SQL syntax help             | Use to get help for SQL statements like SELECT, INSERT, etc. |
| \q                   | General          | Quit psql                   | Use to exit the session.                                     |
| \c / \connect        | Connection       | Connect to another database | Use to switch databases without restarting psql.             |
| \conninfo            | Connection       | Show connection info        | Use to verify current database and user.                     |
| \dt                  | Informational    | List tables                 | Use to view all tables in the current schema.                |
| \d                   | Informational    | Describe object             | Use to inspect structure of tables, views, indexes, etc.     |
| \dv                  | Informational    | List views                  | Use to see all views in the current schema.                  |
| \df                  | Informational    | List functions              | Use to explore defined functions.                            |
| \du                  | Informational    | List roles/users            | Use to check user roles and permissions.                     |
| \dx                  | Informational    | List extensions             | Use to see installed extensions like postgis, uuid-ossp.     |
| \l                   | Informational    | List databases              | Use to view all databases on the server.                     |
| \r                   | Query Buffer     | Reset query buffer          | Use to clear the current query before rewriting.             |
| \p                   | Query Buffer     | Show query buffer           | Use to preview the current query before execution.           |
| \e                   | Query Buffer     | Edit query buffer           | Use to open the query in an external editor.                 |
| \g                   | General          | Execute query               | Use to run the query or send output to file.                 |
| \gx                  | General          | Execute with expanded output| Use when results are wide or hard to read.                   |
| \gexec               | General          | Execute query results       | Use for dynamic SQL execution from query results.            |
| \gset                | General          | Store result in variables   | Use to capture query output into psql variables.             |
| \watch               | General          | Re-run query periodically   | Use for monitoring changes like row counts.                  |
| \s                   | Query Buffer     | Show/save command history   | Use to review or archive session commands.                   |
| \w                   | Query Buffer     | Write buffer to file        | Use to save your current query for later use.                |
| \i                   | Input/Output     | Execute file commands       | Use for running SQL scripts.                                 |
| \ir                  | Input/Output     | Execute relative file       | Use for modular scripts with relative paths.                 |
| \o                   | Input/Output     | Redirect query output       | Use to save results to a file or pipe.                       |
| \echo                | Input/Output     | Print to console            | Use for debugging or messaging in scripts.                   |
| \qecho               | Input/Output     | Print to redirected output  | Use to write messages to output file set by \o.              |
| \copy                | Input/Output     | Perform SQL COPY            | Use to import/export data between client and server.         |
| \if / \elif / \else  | Conditional      | Control flow in scripts     | Use for conditional logic in automated scripts.              |
| \endif               | Conditional      | End conditional block       | Use to close conditional logic.                              |
| \set                 | Variables        | Set internal variable       | Use to define reusable values in scripts.                    |
| \unset               | Variables        | Remove variable             | Use to clean up or reset script variables.                   |
| \prompt              | Variables        | Prompt user for input       | Use to interactively set variables.                          |
| \timing              | OS Interaction   | Toggle timing               | Use to measure query performance.                            |
| \!                   | OS Interaction   | Run shell command           | Use to execute OS commands from within psql.                 |
| \cd                  | OS Interaction   | Change working directory    | Use to navigate file system for script access.               |
| \getenv / \setenv    | OS Interaction   | Manage environment variables| Use to fetch or set shell environment variables.             |
| \a                   | Formatting       | Toggle unaligned output     | Use for CSV or plain-text output.                            |
| \x                   | Formatting       | Toggle expanded output      | Use when rows are too wide for standard display.             |
| \f                   | Formatting       | Set field separator         | Use to customize output format for exports.                  |
| \pset                | Formatting       | Set output options          | Use to fine-tune display (e.g., border, null text).          |
| \C                   | Formatting       | Set table title             | Use to label query output tables.                            |
| \T                   | Formatting       | Set HTML table attributes   | Use for HTML output customization.                           |
| \H                   | Formatting       | Toggle HTML output          | Use to generate HTML-formatted results.                      |
| \lo_import / export  | Large Objects    | Manage BLOBs                | Use to handle binary data like images or files.              |
| \lo_list / unlink    | Large Objects    | List or delete BLOBs        | Use to inspect or remove large objects.                      |
+----------------------+------------------+-----------------------------+--------------------------------------------------------------+

```
