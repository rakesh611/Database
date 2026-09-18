# MySQL — Configuration

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

## 1. Configuration workflow

```text
Read current value
      ↓
Record baseline
      ↓
Understand scope
      ↓
Change one parameter
      ↓
Validate syntax
      ↓
Reload/restart if required
      ↓
Verify runtime value
      ↓
Monitor impact
```

## 2. Linux evidence

```bash
systemctl cat {service}
systemctl show {service} --property=Environment
ss -lntp | grep {port}
df -hT
free -h
ulimit -a
```

## 3. Configuration safety

Before changing a production file:

```bash
cp -a <config-file> <config-file>.bak.$(date +%F-%H%M%S)
```

Keep a change record:

```text
Parameter:
Old value:
New value:
Reason:
Expected impact:
Validation:
Rollback:
Owner:
Change window:
```

## 4. Typical configuration areas

- Listener address and port
- Authentication
- Connection limits
- Memory/cache
- Logging
- Timeouts
- Durability
- Replication
- TLS
- Monitoring
- File locations

## 5. Verification principle

A value in a configuration file is not necessarily the effective runtime value. Always verify using the database's runtime metadata or diagnostic interface after a reload/restart.

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
