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
Extensions

```
# 🔧 pg_repack

What it does: Reorganizes tables and indexes to remove bloat without requiring heavy locks.

Why use it: Alternative to VACUUM FULL, but works online with minimal disruption.

How it works: Creates a new table, copies data, applies changes via a log table, then swaps.

Create:
CREATE EXTENSION pg_repack;

Precheck:
Table must have a primary key or UNIQUE NOT NULL index.
Ensure enough free disk space (2x the size of table + indexes).

Postcheck:
Monitor logs and verify table size reduction.
Use pgstattuple to confirm bloat removal.
```
```
🧠 plpgsql
What it does: Enables procedural programming in PostgreSQL (loops, conditions, variables).
Why use it: Needed for writing stored procedures, triggers, and complex functions.
How it works: SQL is embedded in procedural blocks using BEGIN...END.
----
Create
CREATE EXTENSION IF NOT EXISTS plpgsql;
----
(Usually preloaded by default)
Precheck/Postcheck: Not applicable—it's a core language.
Supported versions: All PostgreSQL versions (enabled by default)
```
```
📊 pg_stat_statements

What it does: Tracks execution statistics of SQL statements.
Why use it: Helps identify slow queries and optimize performance.
How it works: Collects metrics like execution time, rows returned, and block hits.
----
Create: Add to postgresql.conf:
shared_preload_libraries = 'pg_stat_statements'
Then restart and run:
CREATE EXTENSION pg_stat_statements;

----
Precheck:
Must be added to shared_preload_libraries.
Requires server restart.

Postcheck:
Query pg_stat_statements view to analyze performance.

```
```
⏱️ pg_cron

What it does: Schedules SQL jobs using cron syntax inside PostgreSQL.
Why use it: Automates maintenance, reporting, and recurring tasks.
How it works: Background worker executes scheduled SQL commands.
Create: Add to postgresql.conf:
shared_preload_libraries = 'pg_cron'
CREATE EXTENSION pg_cron;

Precheck:
Requires shared_preload_libraries.
Must be installed in only one database per cluster.

Postcheck:
Monitor cron.job and cron.job_run_details tables.
```

```
🤖 azure_ai
What it does: Connects PostgreSQL to Azure Cognitive Services for AI/ML inference.

Why use it: Enables real-time predictions and analysis directly from SQL.

How it works: Uses REST APIs to send data to Azure AI and return results.

Create:
CREATE EXTENSION azure_ai;

Precheck:
Must be allowlisted in Azure Flexible Server.
Requires Azure credentials and endpoint setup.

Postcheck:
Validate inference results and monitor latency.
```

```
🧹 pg_squeeze

What it does: Automatically removes table bloat using background workers.

Why use it: Like pg_repack, but more efficient and less intrusive.

How it works: Uses logical decoding to rebuild tables with minimal locking.

Create: Add to postgresql.conf:
shared_preload_libraries = 'pg_squeeze'
wal_level = logical
max_replication_slots = 1

Then restart and run:
CREATE EXTENSION pg_squeeze;

Precheck:
Table must have a primary key or identity index.
Register table in squeeze.tables:
INSERT INTO squeeze.tables (tabschema, tabname, schedule) VALUES ('public', 'my_table', ...);

Postcheck:
Monitor squeeze logs and verify table size reduction.
```
