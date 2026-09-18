# MariaDB — Migration

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
