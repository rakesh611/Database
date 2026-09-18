# MariaDB — Monitoring

> Database baseline: 12.3.3  
> Default port: 3306  
> Linux service: `mariadb.service`

## Scope

This chapter is written for Linux Administrator / DBA / DevOps / SRE / Kubernetes / OpenShift work. Commands are examples for a controlled lab and must be adapted to the installed version and environment.

## Assumptions

```text
OS: RHEL 9 or compatible Enterprise Linux
Hostname: db01
Database: MariaDB
Port: 3306
Administrative access: root or sudo
```

## 1. Monitoring layers

```text
Node
 ├─ CPU
 ├─ RAM
 ├─ Disk
 ├─ Network
 └─ Filesystem
      ↓
Database
 ├─ Connections
 ├─ Transactions
 ├─ Locks
 ├─ Cache
 ├─ Query latency
 ├─ Replication
 └─ Errors
```

## 2. Minimum alerts

- service unavailable
- port unavailable
- filesystem near full
- connection saturation
- replication lag
- backup failure
- high error rate
- sustained CPU saturation
- sustained I/O latency
- unexpected restart

## 3. Evidence collection

```bash
date
hostname
uptime
free -h
df -hT
ss -s
systemctl status {service} --no-pager
journalctl -u {service} --since "1 hour ago" --no-pager
```

## 4. Monitoring principle

Alert on symptoms that require action, not every metric that changes.

## MariaDB health queries

```sql
SHOW GLOBAL STATUS LIKE 'Threads_connected';
SHOW GLOBAL STATUS LIKE 'Threads_running';
SHOW GLOBAL STATUS LIKE 'Questions';
SHOW PROCESSLIST;
```

For Galera environments, monitor cluster state, component size, donor/joiner state and flow-control indicators.

## MariaDB-specific operational notes

- Default port: `3306`.
- Service name in this handbook: `mariadb.service`.
- CLI: `mariadb`.
- Data location used as a lab baseline: `/var/lib/mysql`.
- Replication model: Primary/replica binary-log replication; Galera-based synchronous multi-primary clustering.
- HA model: Galera Cluster and MaxScale-based architectures; Kubernetes Operator patterns.

## Validation principle

A command is not considered successful until its effect is verified. Prefer:
```bash
systemctl is-active mariadb.service
ss -lntp | grep 3306
```
plus a database-native health query.

## Version discipline

Do not assume that an older blog post, copied command, or container tag applies to the current release. Record the exact installed version and consult the vendor's current documentation when syntax or behavior is version-sensitive.
