# MySQL — Quick Revision

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

# Quick Revision

## 1. Five-minute revision

```text
Database → Port → Service → CLI → Data path
                  ↓
             Configuration
                  ↓
             Users / Auth
                  ↓
       Backup → Replication → HA
                  ↓
       Monitor → Troubleshoot
```

## 2. Fifteen-minute revision

### Linux
```bash
systemctl status {service}
ss -lntp | grep {port}
df -hT
free -h
vmstat 1 5
iostat -xz 1 5
journalctl -u {service} -n 100
```

### Investigation
```text
Process?
Port?
Authentication?
Connections?
Queries?
Locks?
CPU?
Memory?
I/O?
Storage?
Replication?
Backup?
```

## 3. Thirty-minute lab

1. Start the service.
2. Confirm port.
3. Create a database.
4. Create a least-privilege user.
5. Insert test data.
6. Take a backup.
7. Restore into a test database.
8. Stop/start the service.
9. Inspect logs.
10. Measure basic host metrics.

## 4. Interview revision

Remember:
- Backup ≠ replication.
- HA ≠ DR.
- Listener availability ≠ application health.
- PVC ≠ backup.
- Replica ≠ guaranteed failover.
- Configuration file ≠ effective runtime value.
- High CPU is a symptom, not a root cause.

## 5. Production revision

```text
Observe
→ Preserve evidence
→ Diagnose
→ Recover safely
→ Validate
→ Prevent recurrence
→ Document RCA
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
