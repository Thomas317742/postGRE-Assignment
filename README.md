# PostgreSQL Backup and Recovery Assignment

## 1. PostgreSQL Database

Database used:

`bootcamp`

A `students` table was verified in the database.

## 2. Logical Backup Using pg_dump

A custom-format backup was created using `pg_dump`.

Backup file:

`bootcamp_backup.dump`

Command used:

```bash
pg_dump -U postgres -d bootcamp -F c -f bootcamp_backup.dump
```

## 3. Database Restore Using pg_restore

A new database named `bootcamp_restore` was created and the backup was restored successfully.

Command used:

```bash
pg_restore -U postgres -d bootcamp_restore bootcamp_backup.dump
```

The `students` table was verified after restoration.

## 4. WAL Archiving

WAL archiving was enabled in PostgreSQL.

Configuration verified:

```sql
SHOW wal_level;
```

Result:

`replica`

```sql
SHOW archive_mode;
```

Result:

`on`

The archive command was configured to store WAL files in:

`C:\pg_wal_archive`

WAL archiving was successfully verified using `pg_stat_archiver`.

An archived WAL file was created:

`000000010000000000000004`

## 5. Physical Base Backup

A physical base backup was created using `pg_basebackup`.

Command used:

```bash
pg_basebackup -U postgres -D "C:\pg_basebackup" -Fp -Xs -P
```

The backup completed successfully at 100%.

The backup contains:

* `backup_label`
* `backup_manifest`
* `base`
* `global`
* `pg_wal`
* `PG_VERSION`

The PostgreSQL version in the base backup was verified as:

`18`

## Conclusion

The PostgreSQL backup and recovery procedures were successfully completed and verified, including logical backup, restoration, WAL archiving, and physical base backup.
