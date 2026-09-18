# MariaDB — Logging

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

## 1. Log sources

Investigate:
- systemd journal
- database error log
- authentication log
- audit log
- slow-query log
- replication log/evidence
- kernel/storage messages

## 2. Linux commands

```bash
journalctl -u {service} --since "30 min ago"
journalctl -k --since "30 min ago"
dmesg -T | tail -100
```

## 3. Incident correlation

Use timestamps:

```text
09:10 application latency increases
09:11 database connections increase
09:12 disk latency increases
09:13 replication lag begins
```

Correlating timestamps often reveals the causal chain.

## 4. Do not do this

Do not delete logs during an incident simply to recover disk space without first preserving evidence and understanding log rotation requirements.

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
