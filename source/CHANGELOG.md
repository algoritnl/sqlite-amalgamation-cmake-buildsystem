# Changelog

## SQLite Release 3.47.2 On 2024-12-07

1. Fix a problem in text-to-floating-point conversion for SQLite that can cause values between '1.8446744073709550592eNNN' and '1.8446744073709551609eNNN' for any exponent NNN to be rendered incorrectly. In other words, some numeric text values where the first 16 significant digits are '1844674407370955' might be converted into the wrong floating-point value. See forum thread 569a7209179a7f5e. This problem only arises on x64 and i386 hardware. The problem was introduced in 3.47.0.
2. Other minor bug fixes.

## SQLite Release 3.47.1 On 2024-11-25

1. Fix the makefiles so that they once again honored DESTDIR for the "install" target.
2. Add the SQLITE_IOCAP_SUBPAGE_READ capability to the VFS, to work around issues on some non-standard VFSes caused by making SQLITE_DIRECT_OVERFLOW_READ the default in version 3.45.0.
3. Fix problems with line endings in the new sqlite3_rsync.exe utility on Windows.
4. Fix incorrect answers to certain obscure IN queries caused by new query optimizations added in the 3.47.0 release.
5. Other minor bug fixes.

## SQLite Release 3.47.0 On 2024-10-21

1. Allow arbitrary expressions in the second argument to the RAISE function.
2. If the RHS of the ->> operator is negative, then access array elements counting from the right.
3. Fix a problem with rolling back hot journal files in the seldom-used unix-dotfile VFS.
4. FTS5 tables can now be dropped even if they use a non-standard tokenizer that has not been registered.
5. Fix the group_concat() aggregate function so that it returns an empty string, not a NULL, if it receives a single input value which is an empty string.
6. Enhance the generate_series() table-valued function so that it is able to recognize and use constraints on its output value.
7. Preupdate hooks now recognize when a column added by ALTER TABLE ADD COLUMN has a non-null default value.
8. Performance optimizations:
    1. Improved reuse of subqueries associated with the IN operator, especially when the IN operator has been duplicated due to predicate push-down.
    2. Use a Bloom filter on subqueries on the right-hand side of the IN operator, in cases where that seems likely to improve performance.
    3. Ensure that queries like "SELECT func(a) FROM tab GROUP BY 1" only invoke the func() function once per row.
    4. No attempt is made to create automatic indexes on a column that is known to be non-selective because of its use in other indexes that have been analyzed.
    5. Adjustments to the query planner so that it produces better plans for star queries with a large number of dimension tables.
    6. Add the "order-by-subquery" optimization, that seeks to disable sort operations in outer queries if the desired order is obtained naturally due to ORDER BY clauses in subqueries.
    7. The "indexed-subtype-expr" optimization strives to use expressions that are part of an index rather than recomputing the expression based on table values, as long as the query planner can prove that the subtype of the expression will never be used.
    8. Miscellaneous coding tweaks for faster runtimes.
9. Enhancements to SQLite-related command-line programs:
    1. Add the experimental sqlite3_rsync program.
    2. Add extension functions median(), percentile(), percentile_cont(), and percentile_disc() to the CLI.
    3. Add the .www dot-command to the CLI.
    4. The sqlite3_analyzer utility now provides a break-out of statistics for WITHOUT ROWID tables.
    5. The sqldiff utility avoids creating an empty database if its second argument does not exist.
10. Enhance the sqlite_dbpage table-valued function such that INSERT can be used to increase or decrease the size of the database file.
11. SQLite no longer makes any use of the "long double" data type, as hardware support for long double is becoming less common and long double creates challenges for some compiler tool chains. Instead, SQLite uses [Dekker's algorithm](https://csclub.uwaterloo.ca/~pbarfuss/dekker1971.pdf) when extended precision is needed.
12. The TCL Interface for SQLite supports TCL9. Everything probably still works for TCL 8.5 and later, though this is not guaranteed. Users are encouraged to upgrade to TCL9.
13. JavaScript/WASM:
    1. Fix a corruption-causing bug in the JavaScript "opfs" VFS.
    2. Correct "mode=ro" handling for the "opfs" VFS.
    3. Work around a couple of browser-specific OPFS quirks.
14. FTS5 Changes:
    1. Add the fts5_tokenizer_v2 API and the locale=1 option, for creating custom locale-aware tokenizers and fts5 tables that may take advantage of them.
    2. Add the contentless_unindexed=1 option, for creating contentless fts5 tables that store the values of any UNINDEXED columns persistently in the database.
    3. Allow an FTS5 table to be dropped even if it uses a custom tokenizer whose implementation is not available.

## SQLite Release 3.46.1 On 2024-08-13

1. Improved robustness while parsing the tokenize= arguments in FTS5. Forum post 171bcc2bcd.
2. Enhancements to covering index prediction in the query planner. Add early detection of over-prediction of covering indexes so that sqlite3_prepare() will return an error rather than just generate bad bytecode. Forum post e60e4c295d22f8ce.
3. Do not let the number of terms on a VALUES clause be limited by SQLITE_LIMIT_COMPOUND_SELECT, even if the VALUES clause contains elements that appear to be variables due to double-quoted string literals.
4. Fix the window function version of group_concat() so that it returns an empty string if it has one or more empty string inputs.
5. In FTS5 secure-delete mode, fix false-positive integrity-check reports about corrupt indexes.
6. Syntax errors in ALTER TABLE should always return SQLITE_ERROR. In some cases, they were formerly returning SQLITE_INTERNAL.
7. JavaScript/WASM:
    1. Fix a corruption-causing bug in the JavaScript "opfs" VFS.
    2. Work around a couple of browser-specific OPFS quirks.
8. Other minor fixes.

## SQLite Release 3.46.0 On 2024-05-23

1. Enhance PRAGMA optimize in multiple ways, to make it simpler to use:
    1. PRAGMA optimize automatically implements a temporary analysis limit to prevent excess runtime on large databases.
    2. Added the new 0x10000 bitmask option to check for updates on all tables.
    3. Automatically re-analyze tables that do not have sqlite_stat1 entries.
2. Enhancements to the date and time functions:
    1. The strftime() SQL function now supports %G, %g, %U, and %V.
    2. New modifiers 'ceiling' and 'floor' control the algorithm used to resolve ambiguous dates when shifting a date by an integer number of months and/or years.
    3. The 'utc' and 'localtime' modifiers are now no-ops if SQLite knows that the time is already in UTC or in the localtime, respectively.
3. Add support for underscore ("_") characters between digits in numeric literals.
4. Add the json_pretty() SQL function.
5. Query planner improvements:
    1. The "VALUES-as-coroutine" optimization enables INSERT statements with thousands of rows in the VALUES clause to parse and run in about half the time and using about half as much memory.
    2. Allow the use of an index for queries like "SELECT count(DISTINCT col) FROM ...", even if the index records are not smaller than the table records.
    3. Improved recognition of cases where the value of an SQL function is constant because all its arguments are constant.
    3. Enhance the WHERE-clause push-down optimization so that it is able to push down WHERE clause terms containing uncorrelated subqueries.
6. Allocate additional memory from the heap for the SQL parser stack if that stack overflows, rather than reporting a "parser stack overflow" error.
7. JSON changes:
    1. Allow ASCII control characters within JSON5 string literals.
    2. Fix the -> and ->> operators so that when the right-hand side operand is a string that looks like an integer it is still treated as a string, because that is what PostgreSQL does.
8. Allow large hexadecimal literals to be used as the DEFAULT value to a table column.

## SQLite Release 3.45.3 On 2024-04-15

1. Fix a long-standing bug (going back to version 3.24.0) that might (rarely) cause the "old.*" values of an UPDATE trigger to be incorrect if that trigger fires in response to an UPSERT. Forum post 284955a3cd454a15.
2. Fix a bug in sum() that could cause it to return NULL when it should return Infinity. Forum post 23b8688ef4.
3. Other trifling corrections and compiler warning fixes that have come up since the previous patch release. See the timeline for details.

## SQLite Release 3.45.2 On 2024-03-12

1. Fix an error in UPSERT, introduced by enhancement 3a in version 3.35.0 (2021-03-12), that could cause an index to get out-of-sync with its table. Forum thread 919c6579c8.
2. Reduce the scope of the NOT NULL strength reduction optimization that was added as item 8e in version 3.35.0 (2021-03-12). The optimization was being attempted in some contexts where it did not work, resulting in incorrect query results. Forum thread 440f2a2f17.
3. Other trifling corrections and compiler warning fixes that have come up since the previous patch release. See the timeline for details.

## SQLite Release 3.45.1 On 2024-01-30

1. Restore the JSON BLOB input bug, and promise to support the anomaly in subsequent releases, for backward compatibility.
2. Fix the PRAGMA integrity_check command so that it works on read-only databases that contain FTS3 and FTS5 tables. This resolves an issue introduced in version 3.44.0 but was undiscovered until after the 3.45.0 release.
3. Fix issues associated with processing corrupt JSONB inputs:
    1. Prevent exponential runtime when converting a corrupt JSONB into text.
    2. Fix a possible read of one byte past the end of the JSONB blob when converting a corrupt JSONB into text.
    3. Enhanced testing using jfuzz to prevent any future JSONB problems such as the above.
4. Fix a long-standing bug in which a read of a few bytes past the end of a memory-mapped segment might occur when accessing a craftily corrupted database using memory-mapped database.
5. Fix a long-standing bug in which a NULL pointer dereference might occur in the bytecode engine due to incorrect bytecode being generated for a class of SQL statements that are deliberately designed to stress the query planner but which are otherwise pointless.

## SQLite Release 3.45.0 On 2024-01-15

1. Added the SQLITE_RESULT_SUBTYPE property for application-defined SQL functions. All application defined SQL functions that invokes sqlite3_result_subtype() must be registered with this new property. Failure to do so might cause the call to sqlite3_result_subtype() to behave as a no-op. Compile with -DSQLITE_STRICT_SUBTYPE=1 to cause an SQL error to be raised if a function that is not SQLITE_RESULT_SUBTYPE tries invokes sqlite3_result_subtype(). The use of -DSQLITE_STRICT_SUBTYPE=1 is a recommended compile-time option for every application that makes use of subtypes.
2. Enhancements to the JSON SQL functions:
    1. All JSON functions are rewritten to use a new internal parse tree format called JSONB. The new parse-tree format is serializable and hence can be stored in the database to avoid unnecessary re-parsing whenever the JSON value is used.
    2. New versions of JSON-generating functions generate binary JSONB instead of JSON text.
    3. The json_valid() function adds an optional second argument that specifies what it means for the first argument to be "well-formed".
3. Add the FTS5 tokendata option to the FTS5 virtual table.
4. The SQLITE_DIRECT_OVERFLOW_READ optimization is now enabled by default. Disable it at compile-time using -DSQLITE_DIRECT_OVERFLOW_READ=0.
5. Query planner improvements:
    1. Do not allow the transitive constraint optimization to trick the query planner into using a range constraint when a better equality constraint is available. (Forum post 2568d1f6e6.)
    2. The query planner now does a better job of disregarding indexes that ANALYZE identifies as low-quality. (Forum post 6f0958b03b.)
6. Increase the default value for SQLITE_MAX_PAGE_COUNT from 1073741824 to 4294967294.
7. Enhancements to the CLI:
    1. Improvements to the display of UTF-8 content on Windows
    2. Automatically detect playback of ".dump" scripts and make appropriate changes to settings such as ".dbconfig defensive off" and ".dbconfig dqs_dll on".

## SQLite Release 3.44.2 On 2023-11-24

1. Fix a mistake in the CLI that was introduced by the fix (item 15 above) in 3.44.1.
2. Fix a problem in FTS5 that was discovered during internal fuzz testing only minutes after the 3.44.1 release was tagged.
3. Fix incomplete assert() statements that the fuzzer discovered the day after the previous release.
4. Fix a couple of harmless compiler warnings that appeared in debug builds with GCC 16.

## SQLite Release 3.44.1 On 2023-11-22

1. Change the CLI so that it uses UTF-16 for console I/O on Windows. This enables proper display of unicode text on old Windows7 machines.
2. Other obscure bug fixes.

## SQLite Release 3.44.0 On 2023-11-01

1. Aggregate functions can now include an ORDER BY clause after their last parameter. The arguments to the function are processed in the order specified. This can be important for functions like string_agg() and json_group_array().
2. Add support for the concat() and concat_ws() scalar SQL functions, compatible with PostgreSQL, SQLServer, and MySQL.
3. Add support for the string_agg() aggregate SQL function, compatible with PostgreSQL and SQLServer.
4. New conversion letters on the strftime() SQL function: %e %F %I %k %l %p %P %R %T %u
5. Add new C-language APIs: sqlite3_get_clientdata() and sqlite3_set_clientdata().
6. Many errors associated with CREATE TABLE are now raised when the CREATE TABLE statement itself is run, rather than being deferred until the first time the table is actually used.
7. The PRAGMA integrity_check command now verifies the consistency of the content in various built-in virtual tables using the new xIntegrity method. This works for the FTS3, FTS4, FTS5, RTREE, and GEOPOLY extensions.
8. The SQLITE_DBCONFIG_DEFENSIVE setting now prevents PRAGMA writable_schema from being turned on. Previously writable_schema could be turned on, but would not actually allow the schema to be writable. Now it simply cannot be turned on.
9. Tag the built-in FTS3, FTS4, FTS5, RTREE, and GEOPOLY virtual tables as SQLITE_VTAB_INNOCUOUS so that they can be used inside of triggers in high-security deployments.
10. The PRAGMA case_sensitive_like statement is deprecated, as its use when the schema contains LIKE operators can lead to reports of database corruption by PRAGMA integrity_check.
11. SQLITE_USE_SEH (Structured Exception Handling) is now enabled by default whenever SQLite is built using the Microsoft C compiler. It can be disabled using -DSQLITE_USE_SEH=0
12. Query planner optimizations:
    1. In partial index scans, if the WHERE clause implies a constant value for a table column, replace occurrences of that table column with the constant. This increases the likelihood of the partial index being a covering index.
    2. Disable the view-scan optimization (added in version 3.42.0 - item 1c) as it was causing multiple performance regressions. In its place, reduce the estimated row count for DISTINCT subqueries by a factor of 8.
13. SQLite now performs run-time detection of whether or not the underlying hardware supports "long double" with precision greater than "double" and uses appropriate floating-point routines depending on what it discovered.
14. The CLI for Windows now defaults to using UTF-8 for both input and output on platforms that support it. The --no-utf8 option is available to disable UTF8 support.

## SQLite Release 3.43.2 On 2023-10-10

1. Fix a couple of obscure UAF errors and an obscure memory leak.
2. Omit the use of the sprintf() function from the standard library in the CLI, as this now generates warnings on some platforms.
3. Avoid conversion of a double into unsigned long long integer, as some platforms do not do such conversions correctly.

## SQLite Release 3.43.1 On 2023-09-11

1. Fix a regression in the way that the sum(), avg(), and total() aggregate functions handle infinities.
2. Fix a bug in the json_array_length() function that occurs when the argument comes directly from json_remove().
3. Fix the omit-unused-subquery-columns optimization (introduced in in version 3.42.0) so that it works correctly if the subquery is a compound where one arm is DISTINCT and the other is not.
4. Other minor fixes.

## SQLite Release 3.43.0 On 2023-08-24

1. Add support for Contentless-Delete FTS5 Indexes. This is a variety of FTS5 full-text search index that omits storing the content that is being indexed while also allowing records to be deleted.
2. Enhancements to the date and time functions:
    1. Added new time shift modifiers of the form ±YYYY-MM-DD HH:MM:SS.SSS.
    2. Added the timediff() SQL function.
3. Added the octet_length(X) SQL function.
4. Added the sqlite3_stmt_explain() API.
5. Query planner enhancements:
    1. Generalize the LEFT JOIN strength reduction optimization so that it works for RIGHT and FULL JOINs as well. Rename it to OUTER JOIN strength reduction.
    2. Enhance the theorem prover in the OUTER JOIN strength reduction optimization so that it returns fewer false-negatives.
6. Enhancements to the decimal extension:
    1. New function decimal_pow2(N) returns the N-th power of 2 for integer N between -20000 and +20000.
    2. New function decimal_exp(X) works like decimal(X) except that it returns the result in exponential notation - with a "e+NN" at the end.
    3. If X is a floating-point value, then the decimal(X) function now does a full expansion of that value into its exact decimal equivalent.
7. Performance enhancements to JSON processing results in a 2x performance improvement for some kinds of processing on large JSON strings.
8. New makefile target "verify-source" checks to ensure that there are no unintentional changes in the source tree. (Works for canonical source code only - not for precompiled amalgamation tarballs.)
9. Added the SQLITE_USE_SEH compile-time option that enables Structured Exception Handling on Windows while working with the memory-mapped shm file that is part of WAL mode processing. This option is enabled by default when building on Windows using Makefile.msc.
10. The VFS for unix now assumes that the nanosleep() system call is available unless compiled with -DHAVE_NANOSLEEP=0.

## SQLite Release 3.42.0 On 2023-05-16

1. Add the FTS5 secure-delete command. This option causes all forensic traces to be removed from the FTS5 inverted index when content is deleted.
2. Enhance the JSON SQL functions to support JSON5 extensions.
3. The SQLITE_CONFIG_LOG and SQLITE_CONFIG_PCACHE_HDRSZ calls to sqlite3_config() are now allowed to occur after sqlite3_initialize().
4. New sqlite3_db_config() options: SQLITE_DBCONFIG_STMT_SCANSTATUS and SQLITE_DBCONFIG_REVERSE_SCANORDER.
5. Query planner improvements:
    1. Enable the "count-of-view" optimization by default.
    2. Avoid computing unused columns in subqueries.
    3. Improvements to the push-down optimization.
6. Enhancements to the CLI:
    1. Add the --unsafe-testing command-line option. Without this option, some dot-commands (ex: ".testctrl") are now disabled because those commands that are intended for testing only and can cause malfunctions misused.
    2. Allow commands ".log on" and ".log off", even in --safe mode.
    3. "--" as a command-line argument means all subsequent arguments that start with "-" are interpreted as normal non-option argument.
    4. Magic parameters ":inf" and ":nan" bind to floating point literals Infinity and NaN, respectively.
    5. The --utf8 command-line option omits all translation to or from MBCS on the Windows console for interactive sessions, and sets the console code page for UTF-8 I/O during such sessions. The --utf8 option is a no-op on all other platforms.
7. Add the ability for application-defined SQL functions to have the same name as join keywords: CROSS, FULL, INNER, LEFT, NATURAL, OUTER, or RIGHT.
8. Enhancements to PRAGMA integrity_check:
    1. Detect and raise an error when a NaN value is stored in a NOT NULL column.
    2. Improved error message output identifies the root page of a b-tree when an error is found within a b-tree.
9. Allow the session extension to be configured to capture changes from tables that lack an explicit ROWID.
10. Added the subsecond modifier to the date and time functions.
11. Negative values passed into sqlite3_sleep() are henceforth interpreted as 0.
12. The maximum recursion depth for JSON arrays and objects is lowered from 2000 to 1000.
13. Extended the built-in printf() function so the comma option now works with floating-point conversions in addition to integer conversions.
14. Miscellaneous bug fixes and performance optimizations

## SQLite Release 3.41.2 On 2023-03-22

1. Multiple fixes for reads past the end of memory buffers (NB: reads not writes) in the following circumstances:
    1. When processing a corrupt database file using the non-standard SQLITE_ENABLE_STAT4 compile-time option.
    2. In the CLI when the sqlite3_error_offset() routine returns an out-of-range value (see also the fix to sqlite3_error_offset() below).
    3. In the recovery extension.
    4. In FTS3 when processing a corrupt database file.
2. Fix the sqlite3_error_offset() so that it does not return out-of-range values when reporting errors associated with generated columns.
3. Multiple fixes in the query optimizer for problems that cause incorrect results for bizarre, fuzzer-generated queries.
4. Increase the size of the reference counter in the page cache object to 64 bits to ensure that the counter never overflows.
5. Fix a performance regression caused by a bug fix in patch release 3.41.1.
6. Fix a few incorrect assert() statements.

## SQLite Release 3.41.1 On 2023-03-10

1. Provide compile-time options -DHAVE_LOG2=0 and -DHAVE_LOG10=0 to enable SQLite to be compiled on systems that omit the standard library functions log2() and log10(), repectively.
2. Ensure that the datatype for column t1.x in "CREATE TABLE t1 AS SELECT CAST(7 AS INT) AS x;" continues to be INT and is not NUM, for historical compatibility.
3. Enhance PRAGMA integrity_check to detect when extra bytes appear at the end of an index record.
4. Fix various obscure bugs reported by the user community. See the timeline of changes for details.

## SQLite Release 3.41.0 On 2023-02-21

1. Query planner improvements:
    1. Make use of indexed expressions within an aggregate query that includes a GROUP BY clause.
    2. The query planner has improved awareness of when an index is a covering index and adjusts predicted runtimes accordingly.
    3. The query planner is more aggressive about using co-routines rather than materializing subqueries and views.
    4. Queries against the built-in table-valued functions json_tree() and json_each() will now usually treat "ORDER BY rowid" as a no-op.
    5. Enhance the ability of the query planner to use indexed expressions even if the expression has been modified by the constant-propagation optimization. (See forum thread 0a539c7.)
2. Add the built-in unhex() SQL function.
3. Add the base64 and base85 application-defined functions as an extension and include that extension in the CLI.
4. Add the sqlite3_stmt_scanstatus_v2() interface. (This interface is only available if SQLite is compiled using SQLITE_ENABLE_STMT_SCANSTATUS.)
5. In-memory databases created using sqlite3_deserialize() now report their filename as an empty string, not as 'x'.
6. Changes to the CLI:
    1. Add the new base64() and base85() SQL functions
    2. Enhanced EXPLAIN QUERY PLAN output using the new sqlite3_stmt_scanstatus_v2() interface when compiled using SQLITE_ENABLE_STMT_SCANSTATUS.
    3. The ".scanstats est" command provides query planner estimates in profiles.
    4. The continuation prompt indicates if the input is currently inside of a string literal, identifier literal, comment, trigger definition, etc.
    5. Enhance the --safe command-line option to disallow dangerous SQL functions.
    6. The double-quoted string misfeature is now disabled by default for CLI builds. Legacy use cases can reenable the misfeature at run-time using the ".dbconfig dqs_dml on" and ".dbconfig dqs_ddl on" commands.
7. Enhance the PRAGMA integrity_check command so that it detects when text strings in a table are equivalent to but not byte-for-byte identical to the same strings in the index.
8. Enhance the carray table-valued function so that it is able to bind an array of BLOB objects.
9. Added the sqlite3_is_interrupted() interface.
10.  Long-running calls to sqlite3_prepare() and similar now invoke the progress handler callback and react to sqlite3_interrupt().
11. The sqlite3_vtab_in_first() and sqlite3_vtab_in_next() functions are enhanced so that they reliably detect if they are invoked on a parameter that was not selected for multi-value IN processing using sqlite3_vtab_in(). They return SQLITE_ERROR instead of SQLITE_MISUSE in this case.
12. The parser now ignores excess parentheses around a subquery on the right-hand side of an IN operator, so that SQLite now works the same as PostgreSQL in this regard. Formerly, SQLite treated the subquery as an expression with an implied "LIMIT 1".
13. Added the SQLITE_FCNTL_RESET_CACHE option to the sqlite3_file_control() API.
14. Makefile improvements:
    1. The new makefile target "sqlite3r.c" builds an amalgamation that includes the recovery extension.
    2. New makefile targets "devtest" and "releasetest" for running a quick developmental test prior to doing a check-in and for doing a full release test, respectively.
15. Miscellaneous performance enhancements.

## SQLite Release 3.40.1 On 2022-12-28

1. Fix the --safe command-line option to the CLI such that it correctly disallows the use of SQL functions like writefile() that can cause harmful side-effects.
2. Fix a potential infinite loop in the memsys5 alternative memory allocator. This bug was introduced by a performance optimization in version 3.39.0.
3. Various other obscure fixes.

## SQLite Release 3.40.0 On 2022-11-16

1. Add support for compiling SQLite to WASM and running it in web browsers. NB: The WASM build and its interfaces are considered "beta" and are subject to minor changes if the need arises. We anticipate finalizing the interface for the next release.
2. Add the recovery extension that might be able to recover some content from a corrupt database file.
3. Query planner enhancements:
    1. Recognize covering indexes on tables with more than 63 columns where columns beyond the 63rd column are used in the query and/or are referenced by the index.
    2. Extract the values of expressions contained within expression indexes where practical, rather than recomputing the expression.
    3. The NOT NULL and IS NULL operators (and their equivalents) avoid loading the content of large strings and BLOB values from disk.
    4. Avoid materializing a view on which a full scan is performed exactly once. Use and discard the rows of the view as they are computed.
    5. Allow flattening of a subquery that is the right-hand operand of a LEFT JOIN in an aggregate query.
4. A new typedef named sqlite3_filename is added and used to represent the name of a database file. Various interfaces are modified to use the new typedef instead of "char*". This interface change should be fully backwards compatible, though it might cause (harmless) compiler warnings when rebuilding some legacy applications.
5. Add the sqlite3_value_encoding() interface.
6. Security enhancement: SQLITE_DBCONFIG_DEFENSIVE is augmented to prohibit changing the schema_version. The schema_version becomes read-only in defensive mode.
7. Enhancements to the PRAGMA integrity_check statement:
    1. Columns in non-STRICT tables with TEXT affinity should not contain numeric values.
    2. Columns in non-STRICT tables with NUMERIC affinity should not contain TEXT values that could be converted into numbers.
    3. Verify that the rows of a WITHOUT ROWID table are in the correct order.
8. Enhance the VACUUM INTO statement so that it honors the PRAGMA synchronous setting.
9. Enhance the sqlite3_strglob() and sqlite3_strlike() APIs so that they are able to accept NULL pointers for their string parameters and still generate a sensible result.
10. Provide the new SQLITE_MAX_ALLOCATION_SIZE compile-time option for limiting the size of memory allocations.
11. Change the algorithm used by SQLite's built-in pseudo-random number generator (PRNG) from RC4 to Chacha20.
12. Allow two or more indexes to have the same name as long as they are all in separate schemas.
13. Miscellaneous performance optimizations result in about 1% fewer CPU cycles used on typical workloads.

## SQLite Release 3.39.4 On 2022-09-29

1. Fix the build on Windows so that it works with -DSQLITE_OMIT_AUTOINIT
2. Fix a long-standing problem in the btree balancer that might, in rare cases, cause database corruption if the application uses an application-defined page cache.
3. Enhance SQLITE_DBCONFIG_DEFENSIVE so that it disallows CREATE TRIGGER statements if one or more of the statements in the body of the trigger write into shadow tables.
4. Fix a possible integer overflow in the size computation for a memory allocation in FTS3.
5. Fix a misuse of the sqlite3_set_auxdata() interface in the ICU Extension.

## SQLite Release 3.39.3 On 2022-09-05

1. Use a statement journal on DML statement affecting two or more database rows if the statement makes use of a SQL functions that might abort. See forum thread 9b9e4716c0d7bbd1.
2. Use a mutex to protect the PRAGMA temp_store_directory and PRAGMA data_store_directory statements, even though they are deprecated and documented as not being threadsafe. See forum post 719a11e1314d1c70.
3. Other bug and warning fixes. See the timeline for details.

## SQLite Release 3.39.2 On 2022-07-21

1. Fix a performance regression in the query planner associated with rearranging the order of FROM clause terms in the presences of a LEFT JOIN.
2. Apply fixes for CVE-2022-35737, Chromium bugs 1343348 and 1345947, forum post 3607259d3c, and other minor problems discovered by internal testing.

## SQLite Release 3.39.1 On 2022-07-13

1. Fix an incorrect result from a query that uses a view that contains a compound SELECT in which only one arm contains a RIGHT JOIN and where the view is not the first FROM clause term of the query that contains the view. forum post 174afeae5734d42d.
2. Fix some harmless compiler warnings.
3. Fix a long-standing problem with ALTER TABLE RENAME that can only arise if the sqlite3_limit(SQLITE_LIMIT_SQL_LENGTH) is set to a very small value.
4. Fix a long-standing problem in FTS3 that can only arise when compiled with the SQLITE_ENABLE_FTS3_PARENTHESIS compile-time option.
5. Fix the build so that is works when the SQLITE_DEBUG and SQLITE_OMIT_WINDOWFUNC compile-time options are both provided at the same time.
6. Fix the initial-prefix optimization for the REGEXP extension so that it works correctly even if the prefix contains characters that require a 3-byte UTF8 encoding.
7. Enhance the sqlite_stmt virtual table so that it buffers all of its output.

## SQLite Release 3.39.0 On 2022-06-25

1. Add (long overdue) support for RIGHT and FULL OUTER JOIN.
2. Add new binary comparison operators IS NOT DISTINCT FROM and IS DISTINCT FROM that are equivalent to IS and IS NOT, respective, for compatibility with PostgreSQL and SQL standards.
3. Add a new return code (value "3") from the sqlite3_vtab_distinct() interface that indicates a query that has both DISTINCT and ORDER BY clauses.
4. Added the sqlite3_db_name() interface.
5. The unix os interface resolves all symbolic links in database filenames to create a canonical name for the database before the file is opened. If the SQLITE_OPEN_NOFOLLOW flag is used with sqlite3_open_v2() or similar, the open will fail if any element of the path is a symbolic link.
6. Defer materializing views until the materialization is actually needed, thus avoiding unnecessary work if the materialization turns out to never be used.
7. The HAVING clause of a SELECT statement is now allowed on any aggregate query, even queries that do not have a GROUP BY clause.
8. Many microoptimizations collectively reduce CPU cycles by about 2.3%.

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
