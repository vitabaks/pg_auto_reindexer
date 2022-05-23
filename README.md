# pg_auto_reindexer
 Automatic reindexing of B-tree indexes (bloat cleanup)

This script is designed to search and automatically repacking (reindex) Bloat indexes with minimal locks. For PostgreSQL <= 11 use [pg_repack](https://github.com/reorg/pg_repack) and [REINDEX CONCURRENTLY](https://www.postgresql.org/docs/current/sql-reindex.html#SQL-REINDEX-CONCURRENTLY) for PostgreSQL >= 12.

#### Example
```
pg_auto_reindexer --index_bloat=20 --index_maxsize=5000 --maintenance_start=0100 --maintenance_stop=0600
```

#### pg_auto_reindexer --help
```
pg_auto_reindexer - Automatic reindexing of B-tree indexes

--pghost=
        PostgreSQL host (default: /var/run/postgresql)

--pgport=
        PostgreSQL port (default: 5432)

--dbname=
        PostgreSQL database (default: all databases)

--dbuser=
        PostgreSQL database user name (default: postgres)

--index_bloat=
        Index bloat in % (default: 30)

--index_minsize=
        Minimum index size in MB (default: 1)
        Exclude indexes less than specified size

--index_maxsize=
        Maximum index size in MB (default: 1000000)
        Exclude indexes larger than specified size

--maintenance_start=HHMM --maintenance_stop=HHMM
        Determine the time range of the maintenance window (24 hour format: %H%M) [ optional ]
        Example: 2200 (22 hours 00 minutes)

--bloat_search_method=
        estimate - index bloat estimation (default)
        pgstattuple - use pgstattuple extension to search bloat indexes (could cause I/O spikes) [ optional ]

-h, --help
        show this help, then exit

-V, --version
        output version information, then exit
```

## Compatibility
all supported PostgreSQL versions


## Dependencies:
`postgresql-<version>-repack` package (for postgresql <= 11)


## Installation
1. Download and copy the `pg_auto_reindexer` script to `/usr/bin/` directory
2. Grant execute rights on the scripts

Example:
```
wget https://raw.githubusercontent.com/vitabaks/pg_auto_reindexer/master/pg_auto_reindexer
sudo mv pg_auto_reindexer /usr/bin/
sudo chown postgres:postgres /usr/bin/pg_auto_reindexer
sudo chmod 750 /usr/bin/pg_auto_reindexer
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

#### If you noticed a bug or a missing feature or just have an idea of how this project could be enhanced, please feel free to file an issue.
