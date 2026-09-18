# MariaDB — OpenShift

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

## 1. OpenShift model

```text
Project/Namespace
      ↓
Service
      ↓
Stateful workload / Operator
      ↓
PVC
      ↓
StorageClass
      ↓
PV / backend
```

## 2. Investigation

```bash
oc get pods -n <namespace> -o wide
oc get svc -n <namespace>
oc get pvc -n <namespace>
oc describe pod <pod> -n <namespace>
oc logs <pod> -n <namespace> --tail=200
oc get events -n <namespace> --sort-by=.lastTimestamp
```

## 3. Production topics

Check:
- SCC / Pod Security
- SecurityContext
- node placement
- storage topology
- backup target
- NetworkPolicy
- routes only when appropriate
- secret management
- operator lifecycle

## 4. Important principle

Do not expose a database through an external Route merely because a Route is available. Use internal Services for application-to-database traffic unless an external database endpoint is a deliberate architecture requirement.

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
