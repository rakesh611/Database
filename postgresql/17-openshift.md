# PostgreSQL — OpenShift

> Database baseline: 18.6  
> Default port: 5432  
> Linux service: `postgresql.service`

## Scope

This chapter is written for Linux Administrator / DBA / DevOps / SRE / Kubernetes / OpenShift work. Commands are examples for a controlled lab and must be adapted to the installed version and environment.

## Assumptions

```text
OS: RHEL 9 or compatible Enterprise Linux
Hostname: db01
Database: PostgreSQL
Port: 5432
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

## PostgreSQL-specific operational notes

- Default port: `5432`.
- Service name in this handbook: `postgresql.service`.
- CLI: `psql`.
- Data location used as a lab baseline: `/var/lib/pgsql/18/data`.
- Replication model: Physical streaming replication; logical replication.
- HA model: Streaming replication plus an external failover/orchestration layer.

## Validation principle

A command is not considered successful until its effect is verified. Prefer:
```bash
systemctl is-active postgresql.service
ss -lntp | grep 5432
```
plus a database-native health query.

## Version discipline

Do not assume that an older blog post, copied command, or container tag applies to the current release. Record the exact installed version and consult the vendor's current documentation when syntax or behavior is version-sensitive.
