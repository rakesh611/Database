# MariaDB — Architecture

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

## 1. Architecture

A production database is a system of processes, memory, durable storage, logs, network endpoints, metadata and recovery mechanisms.

```mermaid
flowchart LR
    A[Client] --> L[Listener]
    L --> S[Database Server]
    S --> C[Connection / Session]
    C --> Q[Parser / Planner / Executor]
    Q --> M[Memory / Cache]
    Q --> D[Data Files]
    Q --> R[Redo/WAL/Binlog]
    R --> B[Backup / Replica]
```

## 2. Investigation layers

```text
Application
   ↓
DNS / Network
   ↓
TCP port
   ↓
Database listener
   ↓
Authentication
   ↓
Session
   ↓
SQL
   ↓
Lock / CPU / Memory / I/O
   ↓
Filesystem
   ↓
Disk / SAN / Cloud volume
```

## 3. Linux process checks

```bash
ps -ef | grep -E 'postgres|mysqld|mariadbd' | grep -v grep
ss -lntp
systemctl status {service}
journalctl -u {service} -n 100 --no-pager
```

## 4. Architecture questions

Ask:
- Which process accepts connections?
- Where is durable data stored?
- What log records recovery information?
- What happens after an unclean shutdown?
- How is a replica synchronized?
- What is the failure domain?
- Which component performs authentication?
- Which metric proves health?

## 5. L3 principle

Never stop at “database is slow.” Break the symptom into connection, query, lock, CPU, memory, I/O, storage latency, replication, or application behavior.

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
