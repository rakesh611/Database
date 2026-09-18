# MySQL — Performance Tuning

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

## 1. Performance method

Do not tune by guesswork.

```text
Define symptom
   ↓
Measure
   ↓
Find bottleneck
   ↓
Change one variable
   ↓
Measure again
```

## 2. Four common bottlenecks

| Layer | Examples |
|---|---|
| CPU | expensive queries, saturation |
| Memory | cache misses, swapping |
| I/O | high latency, queueing |
| Locks | blocking, contention |

## 3. Linux baseline

```bash
uptime
top
free -h
vmstat 1 5
iostat -xz 1 5
pidstat -dru 1 5
```

## 4. Query-level thinking

Ask:
- Is the query using an index?
- Is the plan appropriate?
- Is cardinality estimation wrong?
- Is a lock delaying execution?
- Is the application opening too many connections?
- Is storage latency the real bottleneck?

## 5. Change control

Record:
```text
Before metric:
Change:
Expected improvement:
After metric:
Regression check:
Rollback:
```

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
