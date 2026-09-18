# MariaDB — Kubernetes

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

## 1. Database on Kubernetes

Stateful databases require deliberate treatment of:
- persistent storage
- identity
- stable network names
- startup ordering
- readiness
- backup
- failover
- disruption
- security

## 2. Basic lab objects

```mermaid
flowchart TD
    App[Application] --> S[Service]
    S --> P[Database Pod]
    P --> PVC[PersistentVolumeClaim]
    PVC --> PV[PersistentVolume]
```

## 3. Generic checks

```bash
kubectl get pods -o wide
kubectl get svc
kubectl get pvc
kubectl describe pod <pod>
kubectl logs <pod> --tail=200
kubectl get events --sort-by=.lastTimestamp
```

## 4. Do not assume

A Deployment with one database pod is not automatically HA. A PVC is not automatically a backup. A Pod restart is not a recovery strategy.

## 5. Operator model

For production, evaluate a database operator when it provides tested automation for:
- provisioning
- upgrades
- backups
- failover
- replication
- certificates
- status conditions

Verify operator/database version compatibility before deployment.

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
