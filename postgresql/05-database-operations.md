# PostgreSQL — Database Operations

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

## PostgreSQL daily commands

```bash
sudo -u postgres psql
sudo -u postgres psql -c "\l"
sudo -u postgres psql -c "\du"
sudo -u postgres psql -c "SELECT now();"
```

Useful SQL:
```sql
SELECT current_database();
SELECT current_user;
SELECT version();
SELECT pg_size_pretty(pg_database_size(current_database()));
```

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
