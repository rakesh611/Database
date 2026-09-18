# PostgreSQL — Monitoring

> Database baseline: 18.6  
> Default port: 5432  
> Linux service: `postgresql.service`

## Scope

This chapter is written for Linux Administrator / DBA / DevOps / SRE / Kubernetes / OpenShift work. Commands are examples for a controlled lab and must be adapted to the installed version and environment.

## Assumptions

```text
OS: RHEL 9 or compatible Enterprise Linux
Hostname: db01
Database: PostgreSQL
Port: 5432
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

## PostgreSQL health queries

```sql
SELECT now();
SELECT * FROM pg_stat_activity;
SELECT * FROM pg_stat_replication;
SELECT datname, numbackends FROM pg_stat_database;
```

For production, build dashboards around connections, transactions, locks, cache behavior, query latency, WAL generation and replication state.

## PostgreSQL-specific operational notes

- Default port: `5432`.
- Service name in this handbook: `postgresql.service`.
- CLI: `psql`.
- Data location used as a lab baseline: `/var/lib/pgsql/18/data`.
- Replication model: Physical streaming replication; logical replication.
- HA model: Streaming replication plus an external failover/orchestration layer.

## Validation principle

A command is not considered successful until its effect is verified. Prefer:
```bash
systemctl is-active postgresql.service
ss -lntp | grep 5432
```
plus a database-native health query.

## Version discipline

Do not assume that an older blog post, copied command, or container tag applies to the current release. Record the exact installed version and consult the vendor's current documentation when syntax or behavior is version-sensitive.
