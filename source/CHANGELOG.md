# Changelog

## SQLite Release 3.34.0 On 2020-12-01

1. Added the sqlite3_txn_state() interface for reporting on the current transaction state of the database connection.
2. Enhance recursive common table expressions to support two or more recursive terms as is done by SQL Server, since this helps make queries against graphs easier to write and faster to execute.
3. Improved error messages on CHECK constraint failures.
4. CLI enhancements:
    1. The .read dot-command now accepts a pipeline in addition to a filename.
    2. Added options --data-only and --nosys to the .dump dot-command.
    3. Added the --nosys option to the .schema dot-command.
    4. Table name quoting works correctly for the .import dot-command.
    5. The generate_series(START,END,STEP) table-valued function extension is now built into the CLI.
    6. The .databases dot-command now shows the status of each database file as determined by sqlite3_db_readonly() and sqlite3_txn_state().
    7. Added the --tabs command-line option that sets .mode tabs.
    8. The --init option reports an error if the file named as its argument cannot be opened. The --init option also now honors the --bail option.
5. Query planner improvements:
    1. Improved estimates for the cost of running a DISTINCT operator.
    2. When doing an UPDATE or DELETE using a multi-column index where only a few of the earlier columns of the index are useful for the index lookup, postpone doing the main table seek until after all WHERE clause constraints have been evaluated, in case those constraints can be covered by unused later terms of the index, thus avoiding unnecessary main table seeks.
    3. The new OP_SeekScan opcode is used to improve performance of multi-column index look-ups when later columns are constrained by an IN operator.
6. The BEGIN IMMEDIATE and BEGIN EXCLUSIVE commands now work even if one or more attached database files are read-only.
7. Enhanced FTS5 to support trigram indexes.
8. Improved performance of WAL mode locking primitives in cases where there are hundreds of connections all accessing the same database file at once.
9. Enhanced the carray() table-valued function to include a single-argument form that is bound using the auxiliary sqlite3_carray_bind() interface.
10. The substr() SQL function can now also be called "substring()" for compatibility with SQL Server.
11. The syntax diagrams are now implemented as Pikchr scripts and rendered as SVG for improved legibility and ease of maintenance.

## SQLite Release 3.33.0 On 2020-08-14

1. Support for UPDATE FROM following the PostgreSQL syntax.
2. Increase the maximum size of database files to 281 TB.
3. Extended the PRAGMA integrity_check statement so that it can optionally be limited to verifying just a single table and its indexes, rather than the entire database file.
4. Added the decimal extension for doing arbitrary-precision decimal arithmetic.
5. Enhancements to the ieee754 extension for working with IEEE 754 binary64 numbers.
6. CLI enhancements:
    1. Added four new output modes: "box", "json", "markdown", and "table".
    2. The "column" output mode automatically expands columns to contain the longest utput row and automatically turns ".header" on if it has not been previously set.
    3. The "quote" output mode honors ".separator"
    4. The decimal extension and the ieee754 extension are built-in to the CLI
7. Query planner improvements:
    1. Add the ability to find a full-index-scan query plan for queries using INDEXED BY which previously would fail with "no query solution".
    2. Do a better job of detecting missing, incomplete, and/or dodgy sqlite_stat1 data and generates good query plans in spite of the misinformation.
    3. Improved performance of queries like "SELECT min(x) FROM t WHERE y IN (?,?,?)" assuming an index on t(x,y).
8. In WAL mode, if a writer crashes and leaves the shm file in an inconsistent state, subsequent transactions are now able to recover the shm file even if there are active read transactions. Before this enhancement, shm file recovery that scenario would result in an SQLITE_PROTOCOL error.
