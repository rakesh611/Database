# MariaDB — Upgrade

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
