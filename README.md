## pg_auto_reindexer

#### Automatic reindexing of PostgreSQL indexes (bloat cleanup).

pg_auto_reindexer automatically detects and reindexes bloated B-tree indexes with minimal locking. For PostgreSQL versions 11 and earlier, it uses [pg_repack](https://github.com/reorg/pg_repack); for PostgreSQL 12 and later, it uses [REINDEX CONCURRENTLY](https://www.postgresql.org/docs/current/sql-reindex.html#SQL-REINDEX-CONCURRENTLY).

---
**Why do I need index maintenance?**

Over time, PostgreSQL indexes are prone to bloat. This occurs because updates, deletes, and inserts happen continuously, while concurrent transactions may still hold snapshots of old data. As a result, empty and unused pages accumulate inside the indexes, causing them to grow unnecessarily large. This leads to increased disk usage, slower read operations, and gradual degradation of overall database performance.

Regular monitoring of index health and timely reindexing are essential to keep your database running efficiently and to prevent performance issues from growing unnoticed.

---

## Usage example

```
pg_auto_reindexer --index-bloat=40 --maintenance-start=0100 --maintenance-stop=0600
```

Output example:

```
2025-04-28 01:00:57 INFO: Started index maintenance for database: casdb
2025-04-28 01:00:58 INFO:  no bloat indexes were found
2025-04-28 01:00:58 INFO: Completed index maintenance for database: casdb (released: 0 MB)
2025-04-28 01:01:00 INFO: Started index maintenance for database: crsdb
2025-04-28 01:01:08 INFO:  reindex index carousel.crs_queue_low_head_idx (current size: 180 MB)
2025-04-28 01:01:33 INFO:  completed reindex carousel.crs_queue_low_head_idx (new size: 26 MB, reduced: 85%)
2025-04-28 01:01:33 INFO:  reindex index carousel.crs_queue_low_pk (current size: 209 MB)
2025-04-28 01:01:52 INFO:  completed reindex carousel.crs_queue_low_pk (new size: 17 MB, reduced: 91%)
2025-04-28 01:01:52 INFO:  reindex index carousel.crs_queue_low_head_pk (current size: 266 MB)
2025-04-28 01:01:56 INFO:  completed reindex carousel.crs_queue_low_head_pk (new size: 18 MB, reduced: 93%)
2025-04-28 01:01:56 INFO: Completed index maintenance for database: crsdb (released: 594 MB)
```

#### Help
```
pg_auto_reindexer - Automatic reindexing of B-tree indexes

Usage:
  pg_auto_reindexer [OPTIONS]

Connection options:
  -h, --host=HOSTNAME               PostgreSQL host (default: /var/run/postgresql)
  -p, --port=PORT                   PostgreSQL port (default: 5432)
  -U, --username=USERNAME           PostgreSQL user (default: postgres)
  -d, --dbname=DBNAME               PostgreSQL database for reindexing (default: all databases)

Reindexing options:
  -b, --index-bloat=PERCENT         Index bloat threshold in percent (default: 30)
  -m, --index-minsize=MB            Minimum index size in MB (default: 1)
  -M, --index-maxsize=MB            Maximum index size in MB (default: 1000000)
  -s, --maintenance-start=HHMM      Maintenance window start (24h format)
  -S, --maintenance-stop=HHMM       Maintenance window stop (24h format)
  -t, --bloat-search-method=METHOD  Bloat detection method: estimate | pgstattuple (default: estimate)
  -l, --failed-reindex-limit=N      Max reindex failures before skipping DB (default: 0)

Other options:
  -v, --version                     Show version information and exit
  -?, --help                        Show this help message and exit

Example:
  pg_auto_reindexer --index-bloat=40 --maintenance-start=0100 --maintenance-stop=0600
```

#### Automation (cron)

You can automate regular bloat cleanup using cron. Example crontab entries:
```bash
# pg_auto_reindexer: weekdays, indexes up to 10GB, maintenance window until 6 AM
1 0 * * 1-5 root pg_auto_reindexer --index-bloat=30 --index-maxsize=10240 --maintenance-start=0000 --maintenance-stop=0600
# pg_auto_reindexer: weekends, indexes larger than 10GB, maintenance window all day
1 0 * * 6,0 root pg_auto_reindexer --index-bloat=40 --index-minsize=10240 --maintenance-start=0000 --maintenance-stop=2359
```

Note: Adjust the --index-bloat, --index-minsize, --index-maxsize, and maintenance window parameters based on your environment and operational needs.

## Compatibility
all supported PostgreSQL versions

## Dependencies:

For old PostgreSQL versions (11 and below) the [pg_repack](https://github.com/reorg/pg_repack) extension package must be installed.

## Installation
1. Download and copy the `pg_auto_reindexer` script to `/usr/local/bin/` directory
2. Grant execute rights on the scripts

Example:
```
wget https://raw.githubusercontent.com/vitabaks/pg_auto_reindexer/refs/heads/main/pg_auto_reindexer
sudo mv pg_auto_reindexer /usr/local/bin/
sudo chmod +x /usr/local/bin/pg_auto_reindexer
```

## Logging
By default, the script execution is written in syslog. Get the pg_auto_reindexer log:
```
sudo grep pg_auto_reindexer /var/log/syslog
```

## License
Licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.

## Author
Vitaliy Kukharik (PostgreSQL DBA) vitabaks@gmail.com

## Feedback, bug-reports, requests, ...
Are [welcome](https://github.com/vitabaks/pg_auto_reindexer/issues)!
