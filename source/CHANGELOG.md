# Changelog

## SQLite Release 3.38.5 On 2022-05-06

1. Fix a blunder in the CLI of the 3.38.4 release.

## SQLite Release 3.38.4 On 2022-05-04

1. Fix a byte-code problem in the Bloom filter pull-down optimization added by release 3.38.0 in which an error in the byte code causes the byte code engine to enter an infinite loop when the pull-down optimization encounters a NULL key. Forum thread 2482b32700384a0f.
2. Other minor patches. See the timeline for details.

## SQLite Release 3.38.3 On 2022-04-27

1. Fix a case of the query planner be overly aggressive with optimizing automatic-index and Bloom-filter construction, using inappropriate ON clause terms to restrict the size of the automatic-index or Bloom filter, and resulting in missing rows in the output. Forum thread 0d3200f4f3bcd3a3.
2. Other minor patches. See the timeline for details.

## SQLite Release 3.38.2 On 2022-03-26

1. Fix a user-discovered problem with the new Bloom filter optimization that might cause an incorrect answer when doing a LEFT JOIN with a WHERE clause constraint that says that one of the columns on the right table of the LEFT JOIN is NULL. See forum thread 031e262a89b6a9d2.
2. Other minor patches. See the timeline for details.

## SQLite Release 3.38.1 On 2022-03-12

1. Fix problems with the new Bloom filter optimization that might cause some obscure queries to get an incorrect answer.
2. Fix the localtime modifier of the date and time functions so that it preserves fractional seconds.
3. Fix the sqlite_offset SQL function so that it works correctly even in corner cases such as when the argument is a virtual column or the column of a view.
4. Fix row value IN operator constraints on virtual tables so that they work correctly even if the virtual table implementation relies on bytecode to filter rows that do not satisfy the constraint.
5. Other minor fixes to assert() statements, test cases, and documentation. See the source code timeline for details.

## SQLite Release 3.38.0 On 2022-02-22

1. Added the -> and ->> operators for easier processing of JSON. The new operators are compatible with MySQL and PostgreSQL.
2. The JSON functions are now built-ins. It is no longer necessary to use the -DSQLITE_ENABLE_JSON1 compile-time option to enable JSON support. JSON is on by default. Disable the JSON interface using the new -DSQLITE_OMIT_JSON compile-time option.
3. Enhancements to date and time functions:
    1. Added the unixepoch() function.
    2. Added the auto modifier and the julianday modifier.
4. Rename the printf() SQL function to format() for better compatibility. The original printf() name is retained as an alias for backwards compatibility.
5. Added the sqlite3_error_offset() interface, which can sometimes help to localize an SQL error to a specific character in the input SQL text, so that applications can provide better error messages.
6. Enhanced the interface to virtual tables as follows:
    1. Added the sqlite3_vtab_distinct() interface.
    2. Added the sqlite3_vtab_rhs_value() interface.
    3. Added new operator types SQLITE_INDEX_CONSTRAINT_LIMIT and SQLITE_INDEX_CONSTRAINT_OFFSET.
    4. Added the sqlite3_vtab_in() interface (and related) to enable a virtual table to process IN operator constraints all at once, rather than processing each value of the right-hand side of the IN operator separately.
7. CLI enhancements:
    1. Columnar output modes are enhanced to correctly handle tabs and newlines embedded in text.
    2. Added options like "--wrap N", "--wordwrap on", and "--quote" to the columnar output modes.
    3. Added the .mode qbox alias.
    4. The .import command automatically disambiguates column names.
    5. Use the new sqlite3_error_offset() interface to provide better error messages.
8. Query planner enhancements:
    1. Use a Bloom filter to speed up large analytic queries.
    2. Use a balanced merge tree to evaluate UNION or UNION ALL compound SELECT statements that have an ORDER BY clause.
9. The ALTER TABLE statement is changed to silently ignores entries in the sqlite_schema table that do not parse when PRAGMA writable_schema=ON.

## SQLite Release 3.37.2 On 2022-01-06

1. Fix a bug introduced in version 3.35.0 (2021-03-12) that can cause database corruption if a SAVEPOINT is rolled back while in PRAGMA temp_store=MEMORY mode, and other changes are made, and then the outer transaction commits. Check-in 73c2b50211d3ae26
2. Fix a long-standing problem with ON DELETE CASCADE and ON UPDATE CASCADE in which a cache of the bytecode used to implement the cascading change was not being reset following a local DDL change. Check-in 5232c9777fe4fb13.
3. Other minor fixes that should not impact production builds.

## SQLite Release 3.37.1 On 2021-12-30

1. Fix a bug introduced by the UPSERT enhancements of version 3.35.0 that can cause incorrect byte-code to be generated for some obscure but valid SQL, possibly resulting in a NULL-pointer dereference.
2. Fix an OOB read that can occur in FTS5 when reading corrupt database files.
3. Improved robustness of the --safe option in the CLI.
4. Other minor fixes to assert() statements and test cases.

## SQLite Release 3.37.0 On 2021-11-27

1. STRICT tables provide a prescriptive style of data type management, for developers who prefer that kind of thing.
2. When adding columns that contain a CHECK constraint or a generated column containing a NOT NULL constraint, the ALTER TABLE ADD COLUMN now checks new constraints against preexisting rows in the database and will only proceed if no constraints are violated.
3. Added the PRAGMA table_list statement.
4. CLI enhancements:
    1. Add the .connection command, allowing the CLI to keep multiple database connections open at the same time.
    2. Add the --safe command-line option that disables dot-commands and SQL statements that might cause side-effects that extend beyond the single database file named on the command-line.
    3. Performance improvements when reading SQL statements that span many lines.
5. Added the sqlite3_autovacuum_pages() interface.
6. The sqlite3_deserialize() does not and has never worked for the TEMP database. That limitation is now noted in the documentation.
7. The query planner now omits ORDER BY clauses on subqueries and views if removing those clauses does not change the semantics of the query.
8. The generate_series table-valued function extension is modified so that the first parameter ("START") is now required. This is done as a way to demonstrate how to write table-valued functions with required parameters. The legacy behavior is available using the -DZERO_ARGUMENT_GENERATE_SERIES compile-time option.
9. Added new sqlite3_changes64() and sqlite3_total_changes64() interfaces.
10. Added the SQLITE_OPEN_EXRESCODE flag option to sqlite3_open_v2().
11. Use less memory to hold the database schema.

## SQLite Release 3.36.0 On 2021-06-18

1. Improvement to the EXPLAIN QUERY PLAN output to make it easier to understand.
2. Byte-order marks at the start of a token are skipped as if they were whitespace.
3. An error is raised on any attempt to access the rowid of a VIEW or subquery. Formerly, the rowid of a VIEW would be indeterminate and often would be NULL. The -DSQLITE_ALLOW_ROWID_IN_VIEW compile-time option is available to restore the legacy behavior for applications that need it.
4. The sqlite3_deserialize() and sqlite3_serialize() interfaces are now enabled by default. The -DSQLITE_ENABLE_DESERIALIZE compile-time option is no longer required. Instead, there is a new -DSQLITE_OMIT_DESERIALIZE compile-time option to omit those interfaces.
5. The "memdb" VFS now allows the same in-memory database to be shared among multiple database connections in the same process as long as the database name begins with "/".
6. Back out the EXISTS-to-IN optimization (item 8b in the SQLite 3.35.0 change log) as it was found to slow down queries more often than speed them up.
7. Improve the constant-propagation optimization so that it works on non-join queries.
8. The REGEXP extension is now included in CLI builds.

## SQLite Release 3.35.5 On 2021-04-19

1. Fix defects in the new ALTER TABLE DROP COLUMN feature that could corrupt the database file.
2. Fix an obscure query optimizer problem that might cause an incorrect query result.

## SQLite Release 3.35.4 On 2021-04-02

1. Fix a defect in the query planner optimization identified by item 8b above. Ticket de7db14784a08053.
2. Fix a defect in the new RETURNING syntax. Ticket 132994c8b1063bfb.
3. Fix the new RETURNING feature so that it raises an error if one of the terms in the RETURNING clause references a unknown table, instead of silently ignoring that error.
4. Fix an assertion associated with aggregate function processing that was incorrectly triggered by the push-down optimization.

## SQLite Release 3.35.3 On 2021-03-26

1. Enhance the OP_OpenDup opcode of the bytecode engine so that it works even if the cursor being duplicated itself came from OP_OpenDup. Fix for ticket bb8a9fd4a9b7fce5. This problem only came to light due to the recent MATERIALIZED hint enhancement.
2. When materializing correlated common table expressions, do so separately for each use case, as that is required for correctness. This fixes a problem that was introduced by the MATERIALIZED hint enhancement.
3. Fix a problem in the filename normalizer of the unix VFS.
4. Fix the "box" output mode in the CLI so that it works with statements that returns one or more rows of zero columns (such as PRAGMA incremental_vacuum). Forum post afbbcb5b72.
5. Improvements to error messages generated by faulty common table expressions. Forum post aa5a0431c99e.
6. Fix some incorrect assert() statements.
7. Fix to the SELECT statement syntax diagram so that the FROM clause syntax is shown correctly. Forum post 9ed02582fe.
8. Fix the EBCDIC character classifier so that it understands newlines as whitespace. Forum post 58540ce22dcd.
9. Improvements the xBestIndex method in the implementation of the (unsupported) wholenumber virtual table extension so that it does a better job of convincing the query planner to avoid trying to materialize a table with an infinite number of rows. Forum post b52a020ce4.

## SQLite Release 3.35.2 On 2021-03-17

1. Fix a problem in the appendvfs.c extension that was introduced into version 3.35.0.
2. Ensure that date/time functions with no arguments (which generate responses that depend on the current time) are treated as non-deterministic functions. Ticket 2c6c8689fb5f3d2f
3. Fix a problem in the sqldiff utility program having to do with unusual whitespace characters in a virtual table definition.
4. Limit the new UNION ALL optimization described by item 8c in the 3.35.0 release so that it does not try to make too many new subqueries. See forum thread 140a67d3d2 for details.

## SQLite Release 3.35.1 On 2021-03-15

1. Fix a bug in the new DROP COLUMN feature when used on columns that are indexed and that are quoted in the index definition.
2. Improve the built-in documentation for the .dump command in the CLI.

## SQLite Release 3.35.0 On 2021-03-12

1. Added built-in SQL math functions(). (Requires the -DSQLITE_ENABLE_MATH_FUNCTIONS compile-time option.)
2. Added support for ALTER TABLE DROP COLUMN.
3. Generalize UPSERT:
    1. Allow multiple ON CONFLICT clauses that are evaluated in order,
    2. The final ON CONFLICT clause may omit the conflict target and yet still use DO UPDATE.
4. Add support for the RETURNING clause on DELETE, INSERT, and UPDATE statements.
5. Use less memory when running VACUUM on databases containing very large TEXT or BLOB values. It is no longer necessary to hold the entire TEXT or BLOB in memory all at once.
6. Add support for the MATERIALIZED and NOT MATERIALIZED hints when specifying common table expressions. The default behavior was formerly NOT MATERIALIZED, but is now changed to MATERIALIZED for CTEs that are used more than once.
7. The SQLITE_DBCONFIG_ENABLE_TRIGGER and SQLITE_DBCONFIG_ENABLE_VIEW settings are modified so that they only control triggers and views in the main database schema or in attached database schemas and not in the TEMP schema. TEMP triggers and views are always allowed.
8. Query planner/optimizer improvements:
    1. Enhancements to the min/max optimization so that it works better with the IN operator and the OP_SeekScan optimization of the previous release.
    2. Attempt to process EXISTS operators in the WHERE clause as if they were IN operators, in cases where this is a valid transformation and seems likely to improve performance.
    3. Allow UNION ALL sub-queries to be flattened even if the parent query is a join.
    4. Use an index, if appropriate, on IS NOT NULL expressions in the WHERE clause, even if STAT4 is disabled.
    5. Expressions of the form "x IS NULL" or "x IS NOT NULL" might be converted to simply FALSE or TRUE, if "x" is a column that has a "NOT NULL" constraint and is not involved in an outer join.
    6. Avoid checking foreign key constraints on an UPDATE statement if the UPDATE does not modify any columns associated with the foreign key.
    7. Allow WHERE terms to be pushed down into sub-queries that contain window functions, as long as the WHERE term is made up of entirely of constants and copies of expressions found in the PARTITION BY clauses of all window functions in the sub-query.
9. CLI enhancements:
    1. Enhance the ".stats" command to accept new arguments "stmt" and "vmstep", causing prepare statement statistics and only the virtual-machine step count to be shown, respectively.
    2. Add the ".filectrl data_version" command.
    3. Enhance the ".once" and ".output" commands so that if the destination argument begins with "|" (indicating that output is redirected into a pipe) then the argument does not need to be quoted.
10. Bug fixes:
    1. Fix a potential NULL pointer dereference when processing a syntactically incorrect SELECT statement with a correlated WHERE clause and a "HAVING 0" clause. (Also fixed in the 3.34.1 patch release.)
    2. Fix a bug in the IN-operator optimization of version 3.33.0 that can cause an incorrect answer.
    3. Fix incorrect answers from the LIKE operator if the pattern ends with "%" and there is an "ESCAPE '_'" clause.

## SQLite Release 3.34.1 On 2021-01-20

1. Fix a potential use-after-free bug when processing a a subquery with both a correlated WHERE clause and a "HAVING 0" clause and where the parent query is an aggregate.
2. Fix documentation typos
3. Fix minor problems in extensions.

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
