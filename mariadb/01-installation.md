# MariaDB — Installation

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

## Verified baseline
- Documentation checked: 2026-09-18.
- MariaDB Community Server 12.3.3 is the latest maintenance release reported by MariaDB in August 2026.
- MariaDB has moved to yearly LTS releases; 12.3 is the 2026 LTS series.
- RHEL 9 packages are published by MariaDB.

## Repository setup

Use MariaDB's official repository setup method for the selected series:

```bash
cat /etc/redhat-release
uname -m
dnf repolist
curl -LsS https://r.mariadb.com/downloads/mariadb_repo_setup | sudo bash -s -- --mariadb-server-version=12.3
dnf install -y MariaDB-server MariaDB-client
systemctl enable --now mariadb
systemctl status mariadb --no-pager
```

## Initial security

```bash
mariadb-secure-installation
```

Review every prompt rather than blindly accepting defaults.

## Verification

```bash
mariadb --version
systemctl is-active mariadb
ss -lntp | grep 3306
mariadb -e "SELECT VERSION();"
```

> Production note: verify the selected MariaDB series and repository setup command against the current MariaDB documentation before production deployment.

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
