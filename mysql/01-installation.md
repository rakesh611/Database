# MySQL — Installation

> Database baseline: 9.7 LTS  
> Default port: 3306  
> Linux service: `mysqld.service`

## Scope

This chapter is written for Linux Administrator / DBA / DevOps / SRE / Kubernetes / OpenShift work. Commands are examples for a controlled lab and must be adapted to the installed version and environment.

## Assumptions

```text
OS: RHEL 9 or compatible Enterprise Linux
Hostname: db01
Database: MySQL
Port: 3306
Administrative access: root or sudo
```

## Verified baseline
- Documentation checked: 2026-09-18.
- MySQL 9.7 is the current LTS series.
- MySQL 8.4 is also an LTS series.
- The official MySQL Yum repository for Enterprise Linux currently enables MySQL 9.7 LTS by default.

## Fresh RHEL 9 workflow

The official repository setup RPM is platform/version-specific. Download the current MySQL Yum repository RPM from the official MySQL repository page, then install it:

```bash
cat /etc/redhat-release
uname -m
dnf repolist
dnf install -y ./mysql97-community-release-<platform>-<version>.noarch.rpm
dnf repolist enabled | grep mysql
dnf install -y mysql-community-server
systemctl enable --now mysqld
systemctl status mysqld --no-pager
```

Do not invent the repository RPM version. Select the current filename from the official MySQL Yum Repository download page.

## Initial root password

On a fresh initialization, MySQL records a temporary root password in the error log. Verify the exact location on the installed package:

```bash
grep 'temporary password' /var/log/mysqld.log
mysql -uroot -p
```

Then change it:

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'ChangeMe_Strong_123!';
```

## Verification

```bash
mysql --version
systemctl is-active mysqld
ss -lntp | grep 3306
mysql -uroot -p -e "SELECT VERSION();"
```

> Production note: use the current MySQL 9.7 installation manual for repository filenames, supported platforms, and release-track selection.

## MySQL-specific operational notes

- Default port: `3306`.
- Service name in this handbook: `mysqld.service`.
- CLI: `mysql`.
- Data location used as a lab baseline: `/var/lib/mysql`.
- Replication model: Binary-log based asynchronous/semi-synchronous replication; Group Replication for HA topologies.
- HA model: MySQL InnoDB Cluster/Group Replication and Router patterns.

## Validation principle

A command is not considered successful until its effect is verified. Prefer:
```bash
systemctl is-active mysqld.service
ss -lntp | grep 3306
```
plus a database-native health query.

## Version discipline

Do not assume that an older blog post, copied command, or container tag applies to the current release. Record the exact installed version and consult the vendor's current documentation when syntax or behavior is version-sensitive.
