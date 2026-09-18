# MySQL — Backup and Restore

> Database baseline: 9.7 LTS  
> Default port: 3306  
> Linux service: `mysqld.service`

## Scope

This chapter is written for Linux Administrator / DBA / DevOps / SRE / Kubernetes / OpenShift work. Commands are examples for a controlled lab and must be adapted to the installed version and environment.

## Assumptions

```text
OS: RHEL 9 or compatible Enterprise Linux
Hostname: db01
Database: MySQL
Port: 3306
Administrative access: root or sudo
```

## 1. Backup objective

A backup is useful only when:
- it completes,
- it is protected,
- it is retained,
- it is monitored,
- and restoration has been tested.

## 2. Recovery model

```text
Backup
  +
Transaction / WAL / Binary Log
  =
Point-in-time recovery capability
```

## 3. PostgreSQL logical backup

```bash
sudo -u postgres pg_dump -Fc appdb > /backup/appdb.dump
sudo -u postgres createdb restore_test
sudo -u postgres pg_restore -d restore_test /backup/appdb.dump
```

## 4. MySQL / MariaDB logical pattern

```bash
mysqldump -u root -p --single-transaction --routines --triggers appdb > /backup/appdb.sql
mysql -u root -p restore_test < /backup/appdb.sql
```

For MariaDB, `mariadb-dump` is the native tool name in current releases.

## 5. Restore test

Never overwrite production during the first restore test. Restore into an isolated database/host and validate:
- object count
- row count
- application connectivity
- permissions
- representative queries

## 6. Backup evidence

Record:
```text
Backup start:
Backup end:
Size:
Exit code:
Checksum:
Retention:
Location:
Encryption:
Restore test date:
Restore test result:
```

## MySQL-specific operational notes

- Default port: `3306`.
- Service name in this handbook: `mysqld.service`.
- CLI: `mysql`.
- Data location used as a lab baseline: `/var/lib/mysql`.
- Replication model: Binary-log based asynchronous/semi-synchronous replication; Group Replication for HA topologies.
- HA model: MySQL InnoDB Cluster/Group Replication and Router patterns.

## Validation principle

A command is not considered successful until its effect is verified. Prefer:
```bash
systemctl is-active mysqld.service
ss -lntp | grep 3306
```
plus a database-native health query.

## Version discipline

Do not assume that an older blog post, copied command, or container tag applies to the current release. Record the exact installed version and consult the vendor's current documentation when syntax or behavior is version-sensitive.
