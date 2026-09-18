# PostgreSQL — Users, Roles and Permissions

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

## 1. Identity model

```text
Human / Application
        ↓
Authentication
        ↓
User / Role
        ↓
Privileges
        ↓
Database / Schema / Object
```

## 2. PostgreSQL lab example

```sql
CREATE ROLE app_readwrite LOGIN PASSWORD 'ChangeMe_Strong_123!';
CREATE DATABASE appdb OWNER app_readwrite;
GRANT CONNECT ON DATABASE appdb TO app_readwrite;
```

## 3. MySQL / MariaDB lab pattern

```sql
CREATE USER 'appuser'@'10.%' IDENTIFIED BY 'ChangeMe_Strong_123!';
GRANT SELECT, INSERT, UPDATE, DELETE ON appdb.* TO 'appuser'@'10.%';
```

Use the exact authentication syntax supported by the installed release.

## 4. Least privilege

Avoid:
```text
Application → superuser
```

Prefer:
```text
Application → dedicated login
                 ↓
            minimum rights
```

## 5. Investigation commands

```sql
-- PostgreSQL
SELECT usename FROM pg_user;

-- MySQL/MariaDB
SELECT User, Host FROM mysql.user;
```

Do not expose password hashes or credentials in tickets, Git repositories or shell history.

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
