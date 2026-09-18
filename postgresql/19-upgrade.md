# PostgreSQL — Upgrade

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

## 1. Upgrade classes

| Type | Typical risk |
|---|---|
| Patch/minor | Lower, but still test |
| Major | Higher; compatibility and migration required |
| OS + database together | Highest operational complexity |

## 2. Pre-upgrade checklist

```bash
uname -a
cat /etc/redhat-release
df -hT
free -h
```

Also capture:
- database version
- extension/plugin versions
- configuration
- backup success
- restore test
- replication status
- application compatibility

## 3. Rollback

A rollback plan must be technically executable, not just “restore the backup.” Decide whether rollback means package downgrade, standby promotion, snapshot restore, dump restore, or application cutback.

## 4. Version-specific warning

Major-version upgrade procedures differ substantially between PostgreSQL, MySQL and MariaDB. Follow the database's official upgrade guide for the exact source and target versions.

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
