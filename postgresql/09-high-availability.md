# PostgreSQL — High Availability

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

## 1. HA is a system

```text
Database
+ Replication
+ Failure Detection
+ Promotion
+ Client Routing
+ Fencing
+ Recovery
= HA
```

## 2. Failure domains

Consider:
- process failure
- host failure
- disk failure
- network partition
- availability-zone failure
- human error
- corrupted data
- credential/TLS failure

## 3. Generic HA flow

```mermaid
flowchart TD
    C[Client] --> R[Router / VIP / Service]
    R --> P[Primary]
    P --> S[Standby]
    S --> R
    P -. failure .-> D[Detection]
    D --> F[Failover]
    F --> R
```

## 4. Important distinction

HA reduces service interruption. Backup/DR addresses recovery from data loss and larger failures. A highly available system can still lose data if an erroneous transaction is replicated everywhere.

## 5. Planned failover checklist

1. Verify replication health.
2. Confirm client routing.
3. Quiesce writes when required.
4. Promote standby using the supported mechanism.
5. Redirect clients.
6. Validate writes.
7. Rebuild or rejoin the old primary safely.
8. Record timings and evidence.

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
