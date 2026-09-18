# MySQL — Security

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

## 1. Security layers

```text
OS hardening
   ↓
Network controls
   ↓
TLS
   ↓
Database authentication
   ↓
Authorization
   ↓
Auditing / logging
   ↓
Secrets management
   ↓
Backup protection
```

## 2. Linux controls

```bash
firewall-cmd --list-all
getenforce
ss -lntp
```

## 3. Least privilege

Use separate identities for:
- application
- monitoring
- backup
- administration
- replication

## 4. Credential handling

Never commit:
```text
passwords
private keys
TLS private material
cloud credentials
database dumps
```

Use environment-specific secret management.

## 5. TLS validation

When TLS is enabled, verify both:
- encryption is active
- certificate identity/trust is correct

Do not treat “port is open” as proof of secure transport.

## 6. Security review

Ask:
- Is the database exposed beyond required networks?
- Are default accounts disabled or controlled?
- Are admin privileges minimized?
- Are backups encrypted?
- Are logs protected?
- Is patching documented?
- Can an operator revoke access quickly?

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
