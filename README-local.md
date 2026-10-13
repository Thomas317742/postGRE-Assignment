# PostgreSQL Backup and Recovery Assignment

## 1. PostgreSQL Database

Database used: `bootcamp`

The `students` table was verified before performing the backup and recovery tasks.

Initial data:

| id | name   | age |
| -- | ------ | --: |
| 1  | Thomas |  25 |

## 2. Logical Backup Using pg_dump

A compressed logical backup was created using `pg_dump`.

Command used:

```bash
pg_dump -U postgres -d bootcamp -F c -f bootcamp_backup.dump
```

The backup was successfully created as `bootcamp_backup.dump`.

The backup contents were verified using:

```bash
pg_restore --list bootcamp_backup.dump
```

The output confirmed that the archive contained the `students` table, table data, sequence, and primary key.

## 3. Point-in-Time Recovery (PITR)

WAL archiving was configured using:

```text
wal_level = replica
archive_mode = on
archive_command = 'copy "%p" "C:/pg_wal_archive/%f"'
```

A physical base backup was created for the PITR test.

A test record was inserted:

```sql
INSERT INTO students (name, age) VALUES ('PITR_Test', 30);
```

The recovery target time was:

```text
2026-10-05 15:01:36+03
```

The test record was deleted from the live database after the target time.

The recovery database was started on port `5433` using `recovery.signal` and the configured `restore_command`.

After recovery, the `students` table contained:

| id | name      | age |
| -- | --------- | --: |
| 1  | Thomas    |  25 |
| 2  | PITR_Test |  30 |

This confirmed that Point-in-Time Recovery successfully restored the database to the selected point in time before the deletion.

## 4. Streaming Replication

A dedicated replication user was created:

```sql
CREATE ROLE replicator WITH REPLICATION LOGIN PASSWORD 'Replicator123!';
```

The PostgreSQL host-based authentication configuration was updated to allow the replication user to connect from localhost.

A standby server was created using:

```bash
pg_basebackup -U replicator -D "C:\pg_standby" -Fp -Xs -P -R -h 127.0.0.1
```

The standby server was started on port `5433`.

Replication status was verified with:

```sql
SELECT pg_is_in_recovery();
```

The result was:

```text
t
```

This confirmed that the server on port `5433` was operating as a standby replica.

## 5. Replication Monitoring

Replication was monitored from the primary database using:

```sql
SELECT application_name, client_addr, state, sync_state
FROM pg_stat_replication;
```

The result showed:

```text
application_name | client_addr | state     | sync_state
walreceiver      | 127.0.0.1  | streaming | async
```

The `streaming` state confirmed that the primary server was actively sending WAL data to the standby server.

The `async` synchronization mode confirmed that asynchronous streaming replication was being used.

## Conclusion

The assignment successfully demonstrated PostgreSQL backup and recovery techniques, including:

* Logical backups using `pg_dump`
* Backup verification using `pg_restore`
* WAL archiving
* Point-in-Time Recovery
* Physical base backups
* Streaming replication
* Replication monitoring using `pg_stat_replication`

All five required PostgreSQL backup, recovery, and replication tasks were successfully completed.
