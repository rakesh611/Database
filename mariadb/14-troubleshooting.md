# MariaDB — Troubleshooting

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

## 1. L2/L3 troubleshooting model

```text
1. Confirm symptom
2. Confirm scope
3. Confirm start time
4. Check recent changes
5. Check service/process
6. Check network
7. Check authentication
8. Check connections
9. Check queries/locks
10. Check CPU/RAM
11. Check I/O/storage
12. Check replication
13. Recover safely
14. Validate
15. RCA
```

## 2. Service down

```bash
systemctl status {service} --no-pager
journalctl -u {service} -n 200 --no-pager
ss -lntp | grep {port}
```

## 3. Port open but login fails

Separate:
```text
TCP connectivity
→ TLS
→ authentication
→ authorization
→ database availability
```

## 4. Database slow

Collect:
```bash
uptime
free -h
vmstat 1 5
iostat -xz 1 5
ss -antp
df -hT
```

Then use native database diagnostics.

## 5. Disk full

```bash
df -hT
df -ih
du -xhd1 /var | sort -h
```

Do not remove database files blindly.

## 6. RCA template

```text
Impact:
Start:
Detection:
Scope:
Timeline:
Immediate cause:
Contributing factors:
Root cause:
Recovery:
Corrective action:
Preventive action:
Monitoring gap:
Validation:
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
