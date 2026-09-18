# PostgreSQL — Installation

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

## Verified baseline
- Documentation checked: 2026-09-18.
- PostgreSQL 18.6 is the current supported major/minor release.
- PostgreSQL 17, 16, 15 and 14 are also supported.
- For RHEL-family systems, the PostgreSQL project provides a Yum repository with supported versions.

## Fresh RHEL/Rocky/Alma Linux workflow

```bash
cat /etc/redhat-release
uname -m
dnf repolist
dnf install -y postgresql-server postgresql-contrib
postgresql-setup --initdb
systemctl enable --now postgresql
systemctl status postgresql --no-pager
sudo -u postgres psql -c "SELECT version();"
```

The exact package version supplied by the OS repository depends on the OS release. For a deliberately selected PostgreSQL major version, use the PostgreSQL Yum repository and follow its current platform-specific repository instructions.

## First verification

```bash
ss -lntp | grep 5432
sudo -u postgres psql -c "SHOW data_directory;"
sudo -u postgres psql -c "SHOW config_file;"
sudo -u postgres psql -c "SHOW hba_file;"
```

## Initial lab database

```bash
sudo -u postgres psql <<'SQL'
CREATE DATABASE appdb;
CREATE USER appuser WITH ENCRYPTED PASSWORD 'ChangeMe_Strong_123!';
GRANT CONNECT ON DATABASE appdb TO appuser;
SQL
```

Do not copy lab passwords into production.

## Verification checklist
1. Service is active.
2. Port 5432 is listening only where intended.
3. Database connection succeeds.
4. Data/config paths are recorded.
5. Authentication rules are reviewed.
6. Backup is tested before production use.

> Production note: always verify the current vendor installation page before applying repository or package commands to a production host.

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
