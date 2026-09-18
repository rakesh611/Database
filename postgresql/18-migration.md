# PostgreSQL — Migration

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

## 1. Migration lifecycle

```text
Discover
 ↓
Assess
 ↓
Backup
 ↓
Test
 ↓
Migrate
 ↓
Validate
 ↓
Cut over
 ↓
Observe
 ↓
Rollback / finalize
```

## 2. Discovery

Record:
- source version
- target version
- schema size
- data size
- largest tables
- users/roles
- extensions/plugins
- replication
- application dependencies
- connection strings
- TLS
- backup strategy

## 3. Validation

Compare:
- row counts
- checksums where appropriate
- indexes
- constraints
- users
- permissions
- application queries
- performance baseline

## 4. Rollback

Define rollback before cutover:
```text
Who can trigger?
What is the trigger?
How is traffic redirected?
How is new data handled?
How is consistency checked?
How long is rollback available?
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
