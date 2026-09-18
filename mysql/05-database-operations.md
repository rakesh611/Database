# MySQL — Database Operations

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

## 1. Daily DBA checklist

```bash
systemctl is-active {service}
ss -lntp | grep {port}
df -hT
free -h
uptime
```

Then check database-native health.

## 2. Operational lifecycle

```text
Create
  ↓
Connect
  ↓
Read / Write
  ↓
Commit
  ↓
Backup
  ↓
Monitor
  ↓
Maintain
  ↓
Recover
```

## 3. Safe operating pattern

For any change:
1. Identify the target.
2. Confirm environment.
3. Capture current state.
4. Execute the smallest change.
5. Validate.
6. Monitor.
7. Record the result.

## 4. Capacity checks

```bash
df -h
df -ih
du -xhd1 {data}
iostat -xz 1 5
vmstat 1 5
```

If `iostat` is unavailable, install the `sysstat` package in the lab.

## 5. Connection investigation

```bash
ss -antp | grep :{port}
```

Separate:
- no listener
- network blocked
- authentication rejected
- connection accepted but query hangs
- connection pool exhaustion

## MySQL daily commands

```bash
systemctl status mysqld --no-pager
mysql -uroot -p
mysql -uroot -p -e "SHOW DATABASES;"
mysql -uroot -p -e "SHOW PROCESSLIST;"
```

Useful SQL:
```sql
SELECT VERSION();
SHOW DATABASES;
SHOW VARIABLES LIKE 'port';
SHOW VARIABLES LIKE 'datadir';
SHOW STATUS LIKE 'Threads_connected';
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
