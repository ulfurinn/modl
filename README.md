# Modl: Go database model & mapping

[![Build Status](https://drone.io/github.com/jmoiron/modl/status.png)](https://drone.io/github.com/jmoiron/modl/latest)

Modl is a library which provides database modelling and mapping.   It is heavily based
on the work in James Cooper's wonderful [gorp](http://github.com/coopernurse/gorp).

**Note**: Modl is under heavy development.  Its public facing interface is still
under some considerable development as I narrow in on how I want the library to
function and how I want using it to feel.  Despite that, modl inherits gorp's
fairly comprehensive test suite, so whatever code is actually in the master
branch is likely to be buildable and to function.

## Goals

Modl's goal is to clean up bits of gorp's API, add some additional features
like query building helpers and additional control over SQL generation, and
to reuse lower level abstractions provided in [sqlx](http://github.com/jmoiron/sqlx).
The driving philosophies behind modl are:

* If something can be done in `database/sql`, do it that way
* Go is not a good declarative language, do not abuse struct tags
* Expose as much as possible to facilitate extension by other libraries
* Avoid abstractions which provide initial convenience but must eventually be abandoned
* Avoid reflect and cache its results where possible, use where necessary

### Features

* Bind struct models to tables
* CRUD helpers for bound structs
* Create schema from database model (great for testing)
* Pre/post insert/update/delete hooks
* Automatic binding of auto increment PKs after insert
* Delete & Fetch by primary keys (w/ multi-key support)
* Sql trace logging
* Bind arbitrary SQL queries to a struct
* Optional optimistic locking using a version column (for update/deletes)

### Differences from Gorp

Since modl is a gorp fork, here are some of its major behavioral differences:

* Field names are mapped to all-lowercase sql equivalents.  Capitalization is
  an artifact of visibility, and this was required for compatibility with `sqlx`.
* No more struct/slice returns, pass pointers to methods instead
* Many panics in gorp are errors in modl
* TypeConverters are removed in favor of implementing `sql.Scanner` & `driver.Valuer`
  for custom types.

## Testing

To use the `test-all` script, set the following environment variables:

```sh
# mysql DSN, like:
MODL_MYSQL_DSN="username:password@/dbname"

# postgres DSN, like:
MODL_POSTGRES_DSN="username=username password=pw dbname=dbname ssl-node=disable"

# sqlite DSN, which is a path
MODL_SQLITE_DSN="/dev/shm/modltest.db"

# optional, will fail the test if any DBs are skipped (for CI, mostly)
MODL_FAIL_ON_SKIP=true
```

In addition to this, you can create an `environ` file in this directory which
will be sourced and ignored by git.  You can continue to use the `MODL_TEST_DSN`
and `MODL_TEST_DIALECT` variables if you want to manually run `go test` or if
you want to run the benchmarks, as described below.

The original README.md follows:


## Supported Databases

Modl relies heavily upon the `database/sql` package, and has a 
[Dialect](dialect.go) interface which can be used to smooth over
differences between databases.  There is a [list of sql drivers](http://code.google.com/p/go-wiki/wiki/SQLDrivers)
on the Go wiki, most of which Modl should be compatible with.  Dialects
are provided for:

* MySQL
* PostgreSQL
* sqlite3

The test suite is continuously run against all of these databases.

## Documentation

TODO.  API Documentation will go onto [godoc](http://godoc.org) when the API
has stabilized.

## Performance ##

Modl performs similar to Gorp.  There are benchmarks in `modl_test.go` which
will benchmark native querying w/ `database/sql` and manual Scanning with what
modl does.  Modl should perform between 2-3% slower than hand-done SQL.

## Contributors

The original contributors to gorp are:

* [@coopernurse](http://github.com/coopernurse) - James Cooper
* [@robfig](http://github.com/robfig) - Rob Figueiredo
* [@sqs](http://github.com/sqs) - Quinn Slack
* matthias-margush - column aliasing via tags

