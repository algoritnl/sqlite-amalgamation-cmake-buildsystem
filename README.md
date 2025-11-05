[![Continuous Integration](https://github.com/algoritnl/sqlite-amalgamation/actions/workflows/ci.yaml/badge.svg)](https://github.com/algoritnl/sqlite-amalgamation/actions/workflows/ci.yaml)

# CMake Build System for the SQLite Amalgamation

## Table of Contents

- [CMake Build System](#cmake-build-system)
  - [Supported Platforms](#supported-platforms)
- [Configuration Options](#configuration-options)
  - [Recommended Options](#recommended-options)
  - [Other Options](#other-options)
- [Software License](#software-license)

## CMake Build System

This repository provides custom CMake Build System generator scripts that help
build the official SQLite amalgamation. The source code of the amalgamation is
included for convenience. In order for the CMake build system to work, all you
need are the following two files:

* CMakeLists.txt
* SQLite3Config.cmake.in

These scripts provide configuring, building, smoke testing, installing,
exporting and packaging of the SQLite amalgamation on Linux systems. They may
or may not work on other systems. GitHub workflow actions have been set up to
verify up to and including the smoke test for ubuntu-latest, macos-latest and
windows-latest.

### Supported Platforms

The build system has been tested on Ubuntu with the GNU and Clang compilers and
on Windows with Visual Studio.

* CMake 3.25.2, 3.30.2
* Ubuntu 22.04 LTS, 24.04 LTS
* GCC 11.3.9, 13.2.0, 14.2.0
* Clang 14.0.0, 18.1.3
* Microsoft Windows 11
* Visual Studio 2019, 2022

## Configuration Options

For most purposes, SQLite can be built just fine using the default compilation
options. However, if required, the compile-time options documented below can
be used to omit SQLite features (resulting in a smaller compiled library size)
or to change the default values of some parameters.

The configuration options understood by the CMake build system listed here is
a small subset of the options that control the SQLite library. See this link
([Compile-time Options](https://sqlite.org/compile.html)) for an exhaustive
list of configuration options. You will have to add them manually to the build
system if you need them.

### Recommended Options

- `SQLITE_DQS:STRING`=**0**:
  value in [**0**-**3**], default = **3**  
  Controls the **Double-Quoted String** literals misfeature in **DDL** (Data
  Definition Language) and **DML** (Data Manipulation Language):
  - 0: Disable **DQS** in **DDL**, disable **DQS** in **DML** (recommended).
  - 1: Disable **DQS** in **DDL**, enable **DQS** in **DML**.
  - 2: Enable **DQS** in **DDL**, disable **DQS** in **DML**.
  - 3: Enable **DQS** in **DDL**, enable **DQS** in **DML** (default).

- `SQLITE_THREADSAFE:STRING`=**0**:
  value in [**0**-**2**], default = **1**.  
  Supported thread models: **0** = single-threaded; **1** = serialized;
  **2** = multi-threaded.  
  The recommendation is only applicable if SQLite is never used by more than a
  single thread at a time, even if each thread has its own database connection.  
  _Note: The library name gets an infix **-st** if the single-threaded model
  is selected and **-mt** for the multi-threaded model._

- `SQLITE_DEFAULT_MEMSTATUS:STRING`=**0**:
  value in [**0**-**1**], default = **1**.  
  This option is used to determine whether or not the features enabled and
  disabled using the **SQL_CONFIG_MEMSTATUS** argument to **sqlite3_config()**
  are available by default.

- `SQLITE_DEFAULT_SYNCHRONOUS:STRING`=**2**:
  value in [**0**-**3**], default = **2**.  
  This option determines the default value of the **PRAGMA synchronous**
  setting. The default and recommended setting is **2** (**FULL**).

- `SQLITE_DEFAULT_WAL_SYNCHRONOUS:STRING`=**1**:
  value in [**0**-**3**], default = **2**.  
  This option determines the default value of the **PRAGMA synchronous** setting
  for database files that open in **WAL mode**.

- `SQLITE_LIKE_DOESNT_MATCH_BLOBS:BOOL`=**ON**:
  value is a **boolean**, default = **OFF**.  
  When this option is set, it means that the **LIKE** and **GLOB** operators
  always **FALSE** if either operand is a BLOB. That simplifies the
  implementation of the LIKE optimization and allows queries that use the
  LIKE optimization to run faster.

- `SQLITE_MAX_EXPR_DEPTH:STRING`=**0**:
  value is an **integer**, default = **-1**.  
  This option determines the maximum expression tree depth. If the value is
  **0**, then no limit is enforced. The recommended value is **0** (no limit).

  *Special value **-1** does not get passed to the build generator,
  so that the source code uses its own default.*

- `SQLITE_ENABLE_COLUMN_METADATA:BOOL`=**OFF**:
  value is a **boolean**, default = **OFF**.  
  This enables some extra APIs that are required by some common systems,
  including Ruby-on-Rails.  
  _This option is mutually exclusive with `SQLITE_OMIT_DECLTYPE`._

- `SQLITE_OMIT_DECLTYPE:BOOL`=**ON**:
  value is a **boolean**, default = **OFF**.  
  By omitting the (seldom-needed) ability to return the declared type of
  columns from the result set of query, prepared statements can be made to
  consume less memory.  
  _This option is mutually exclusive with `SQLITE_ENABLE_COLUMN_METADATA`._

- `SQLITE_OMIT_DEPRECATED:BOOL`=**ON**:
  value is a **boolean**, default = **OFF**.  
  Omitting deprecated interfaces and features will not help SQLite to run any
  faster. It willreduce the library footprint, however. And it is the right
  thing to do.

- `SQLITE_OMIT_PROGRESS_CALLBACK:BOOL`=**ON**:
  value is a **boolean**, default = **OFF**.  
  This option may be defined to omit the capability to issue "progress"
  callbacks during long-running SQL statements. The
  **sqlite3_progress_handler()** API function is not present in the library.

- `SQLITE_OMIT_SHARED_CACHE:BOOL`=**ON**:
  value is a **boolean**, default = **OFF**.  
  This option builds SQLite without support for shared-cache mode.
  The **sqlite3_enable_shared_cache()** is omitted along with a fair amount of
  logic within the B-Tree subsystem associated with shared cache management.

- `SQLITE_USE_ALLOCA:BOOL`=**ON**:
  value is a **boolean**, default = **OFF**.  
  If this option is enabled, then the **alloca()** memory allocator will be used
  in a few situations where it is appropriate. This results in a slightly
  smaller and faster binary. It only works, of course, on systems that support
  **alloca()**.

- `SQLITE_OMIT_AUTOINIT:BOOL`=**ON**:
  value is a **boolean**, default = **OFF**.  
  When built using SQLITE_OMIT_AUTOINIT, SQLite will not automatically initialize
  itself and the application is required to invoke sqlite3_initialize() directly
  prior to beginning use of the SQLite library.

- `SQLITE_STRICT_SUBTYPE:BOOL`=**ON**:
  value is a **boolean**, default = **ON**.  
  This option causes application-defined SQL functions to raise an SQL error if
  they invoke the sqlite3_result_subtype() interface but were not registered
  with the SQLITE_RESULT_SUBTYPE property. This recommended option helps to
  identify problems in the implementation of application-defined SQL functions
  early in the development cycle.

### Other Options

- `SQLITE_ENABLE_API_ARMOR:BOOL`=**OFF**:
  value is a **boolean**.  
  This option activates extra code that attempts to detect misuse of the SQLite API.

- `SQLITE_ENABLE_CARRAY:BOOL`=**OFF**:
  value is a **boolean**.  
  This option enables the carray() extension.

- `SQLITE_ENABLE_DBSTAT_VTAB:BOOL`=**OFF**:
  value is a **boolean**.  
  This option enables the dbstat virtual table.

- `SQLITE_ENABLE_EXPLAIN_COMMENTS:BOOL`=**OFF**:
  value is a **boolean**.  
  This option adds extra logic to SQLite that inserts comment text into the
  output of **EXPLAIN**.

- `SQLITE_ENABLE_FTS3:BOOL`=**OFF**:
  value is a **boolean**.  
  Enable version 3 of the full-text search engine.

- `SQLITE_ENABLE_FTS3_PARENTHESIS:BOOL`=**OFF**:
  value is a **boolean**.  
  This option modifies the query pattern parser in FTS3 such that it supports
  operators **AND** and **NOT** (in addition to the usual **OR** and **NEAR**)
  and also allows query expressions to contain nested parentheses.

- `SQLITE_ENABLE_FTS4:BOOL`=**OFF**:
  value is a **boolean**.  
  Enable versions 3 and 4 of the full-text search engine.

- `SQLITE_ENABLE_FTS5:BOOL`=**OFF**:
  value is a **boolean**.  
  Enable version 5 of the full-text search engine.

- `SQLITE_ENABLE_GEOPOLY:BOOL`=**OFF**:
  value is a **boolean**.  
  When this option is defined, the Geopoly extension is included in the build.

- `SQLITE_ENABLE_ICU:BOOL`=**OFF**:
  value is a **boolean**.  
  This option causes the International Components for Unicode or "ICU" extension
  to SQLite to be added to the build.

- `SQLITE_ENABLE_MATH_FUNCTIONS:BOOL`=**ON**:
  value is a **boolean**.  
  When this option is defined the built-in SQL math functions are added to the
  build automatically.

- `SQLITE_ENABLE_NORMALIZE:BOOL`=**OFF**:
  value is a **boolean**.  
  When this option is defined the sqlite3_normalized_sql() API is included in
  the build.

- `SQLITE_ENABLE_PERCENTILE:BOOL`=**OFF**:
  value is a **boolean**.  
  This option enables the percentile extension.

- `SQLITE_ENABLE_PREUPDATE_HOOK:BOOL`=**OFF**:
  value is a **boolean**.  
  When this option is defined the pre-update hook APIs are included in the build.

- `SQLITE_ENABLE_RBU:BOOL`=**OFF**:
  value is a **boolean**.  
  Enable the code that implements the RBU extension.

- `SQLITE_ENABLE_RTREE:BOOL`=**OFF**:
  value is a **boolean**.  
  This option causes SQLite to include support for the R*Tree index extension.

- `SQLITE_ENABLE_SESSION:BOOL`=**OFF**:
  value is a **boolean**.  
  This option enables the session extension. It requires the pre-update hook APIs.

- `SQLITE_ENABLE_STMTVTAB:BOOL`=**OFF**:
  value is a **boolean**.  
  This compile-time option enables the SQLITE_STMT virtual table logic.

- `SQLITE_MAX_ALLOCATION_SIZE:STRING`=**-1**:
  value is an **integer**.  
  This option sets an upper bound on the size of memory allocations that can be
  requested using sqlite3_malloc64(), sqlite3_realloc64(), and similar.

  *Special value **-1** does not get passed to the build generator,
  so that the source code uses its own default.*

- `SQLITE_OMIT_DESERIALIZE:BOOL`=**OFF**:
  value is a **boolean**.  
  This option causes the sqlite3_serialize() and sqlite3_deserialize()
  interfaces to be omitted from the build.

- `SQLITE_OMIT_JSON:BOOL`=**OFF**:
  value is a **boolean**.  
  When this option is defined the JSON SQL functions are omitted from the
  build.

- `SQLITE_OMIT_LOAD_EXTENSION:BOOL`=**OFF**:
  value is a **boolean**, recommended = **OFF**.  
  This option omits the entire extension loading mechanism from SQLite,
  including **sqlite3_enable_load_extension()** and
  **sqlite3_load_extension()** interfaces.

## Software License

The CMake build system is published under the SQLite Blessing license.
(*SPDX-License-Identifier: blessing*)
