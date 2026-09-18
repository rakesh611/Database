# PostgreSQL — Production Scenarios

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

# Production Scenarios

## Scenario 1 — Database service down

**Symptoms**
- application connection failures
- port unavailable

**Evidence**
```bash
systemctl status {service} --no-pager
journalctl -u {service} -n 200 --no-pager
df -hT
```

**Decision**
Do not restart repeatedly without reading the failure reason.

## Scenario 2 — High CPU

```text
CPU high
 ↓
DB process?
 ↓
Top sessions/queries?
 ↓
Locks?
 ↓
Plan regression?
 ↓
Application traffic change?
```

## Scenario 3 — Replication lag

Check:
- network
- source log rate
- replica CPU
- replica I/O
- locks
- long transactions
- replication errors

## Scenario 4 — Disk 100%

Immediate priorities:
1. Preserve evidence.
2. Identify filesystem.
3. Identify largest safe-to-remove content.
4. Check logs and rotation.
5. Check WAL/redo/binlog growth.
6. Expand storage if required.
7. Validate database health.

## Scenario 5 — Backup succeeded but restore failed

Treat this as a backup incident. Capture the restore error, tool version, backup metadata, target capacity and compatibility. Do not assume the backup is usable until a restore test succeeds.

## Scenario 6 — Kubernetes database Pod CrashLoopBackOff

Check:
```bash
kubectl describe pod <pod>
kubectl logs <pod> --previous
kubectl get pvc
kubectl get events --sort-by=.lastTimestamp
```

Separate application configuration, storage, permissions, probes and database initialization errors.

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
