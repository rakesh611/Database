# PostgreSQL — Security

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
