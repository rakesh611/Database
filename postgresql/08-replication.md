# PostgreSQL — Replication

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

## 1. Replication purpose

Replication can provide:
- read scaling
- standby capacity
- disaster recovery
- maintenance flexibility
- reduced recovery time

Replication is not automatically a backup.

## 2. Generic model

```mermaid
flowchart LR
    P[Primary] --> L[Log Stream]
    L --> R1[Replica 1]
    L --> R2[Replica 2]
    R1 --> APP[Read Workload]
```

## 3. Health dimensions

Measure:
- connectivity
- log shipping
- receiver status
- replay/apply status
- lag
- errors
- disk capacity
- timeline/GTID state where applicable

## 4. Linux evidence

```bash
ss -antp
journalctl -u {service} --since "30 min ago"
df -hT
```

## 5. L3 investigation

If replication lag increases:
1. Confirm network latency.
2. Check primary log generation rate.
3. Check replica CPU.
4. Check replica I/O latency.
5. Check long-running transactions.
6. Check locks.
7. Check replication errors.
8. Compare replay/apply rate with generation rate.

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
