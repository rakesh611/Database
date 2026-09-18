# MySQL — Interview Questions

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

# Interview Questions

## Beginner — 10

1. What is a database?
2. What is the default port?
3. How do you check service status?
4. How do you verify a listener?
5. Where is database data stored?
6. What is a transaction?
7. What is a backup?
8. What is replication?
9. What is HA?
10. What is least privilege?

## Intermediate — 15

1. How do you troubleshoot connection refusal?
2. How do you separate network and authentication failures?
3. How do you investigate high CPU?
4. How do you investigate high I/O latency?
5. What causes connection exhaustion?
6. How do you validate replication?
7. How do you investigate replication lag?
8. Why is replication not a backup?
9. How do you test a backup?
10. What is point-in-time recovery?
11. What is a long-running transaction?
12. How do locks affect availability?
13. How do you safely change configuration?
14. What should be monitored?
15. What belongs in an RCA?

## Advanced — 20

1. Design a primary/standby architecture.
2. Explain failure domains.
3. Explain synchronous vs asynchronous replication.
4. Design backup retention.
5. Define RPO and RTO.
6. Investigate a sudden latency increase.
7. Investigate disk saturation.
8. Diagnose replication divergence.
9. Design a zero/minimal-downtime migration.
10. Design database TLS.
11. Explain connection pooling.
12. Explain query-plan regression.
13. Explain cache pressure.
14. Explain lock contention.
15. Design Kubernetes storage for a stateful database.
16. Explain why a StatefulSet does not itself provide database HA.
17. Design OpenShift network isolation.
18. Plan a major-version upgrade.
19. Build a restore validation process.
20. Write a production RCA.

## Production scenarios — 15

For each scenario answer with:
```text
Symptom → Scope → Evidence → Root cause → Recovery → Validation → Prevention
```

Scenarios:
1. Service down
2. Connection refused
3. Authentication failure
4. Connection saturation
5. High CPU
6. High I/O latency
7. Lock contention
8. Replication lag
9. Replica unavailable
10. Disk full
11. Backup failure
12. Restore failure
13. TLS failure
14. Kubernetes restart loop
15. OpenShift PVC/storage failure

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
