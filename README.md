# SqliteFileSystem
A Zio file system provider that uses a SQLite3 database as a flat file store

appveyor.com/api/projects/status/psh6lcwk51xvjry4?svg=true)](https://ci.appveyor.com/project/atruskie/sqlitefilesystem)

## What

This is an implmentation of the `Zio.FileSystems.IFileSystem` interface that uses an SQLite3 database as a datastore.

Note: this solution is implemented a flat file system - like S3 (or an equivalent object store).
Therefore some operations, like making a directory, have no effect.

## Why

I had the need to switch an existing project from writing file directly to the OS filesystem
and instead into some container files. This need was driven by the need to produce hundreds of
thousands of small files; saving them to disk is was not feasible.

Some container format was needed. I set up a benchmark and tested several different formats:

See <https://github.com/atruskie/index-file-perf-test>

The idea of using SQLite as application container format was inspired by this article:
<SQLite As An Application File Format https://www.sqlite.org/appfileformat.html>.

Given I did not need compression, the benchmark revealed that SQLite3 databases were the fastest method that I tested.

## State and limitations

The implementation is basically complete for reading and writing files. However, please do note these limitations:

- Directories do not exist in this file system. For compatibility, many methods work as if they did exist.
- There is very limited metadata stored for each file: `created`, `accessed`, `modified`
- There is no concept of file locking or sharing implmented
  - This is achieveable with a fair bit more work but is outside the scope of what I need
- Due to the [Microsoft.Data.Sqlite](https://github.com/aspnet/Microsoft.Data.Sqlite) package taking a
  dependency on .NET Standard 2.0, this library requires .NET Standard 2.0 -- unlike Zio itself which depends on
  .NET Standard 1.3.
- There is likely room for performance improvements