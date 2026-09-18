# MariaDB — Security

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
