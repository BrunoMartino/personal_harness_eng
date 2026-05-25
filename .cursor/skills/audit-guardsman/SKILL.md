---
name: audit-guardsman
description: >-
  Ensures privileged and restricted-data operations emit stdout single-line JSON
  audit logs with canonical fields, event naming, and UTC timestamps. Use when
  adding admin flows, RBAC changes, privileged APIs, restricted data access,
  compliance logging, or event_id / event_type instrumentation.
disable-model-invocation: true
---

# Audit Guardsman

## When to use

When adding **privileged functionality** or access to **restricted** data—the “who did what when” audit trail must be adequate.

## Harness docs first

If the project defines logging or auditing standards, align with those first.

## What to audit

1. **Privileged Operations** - Actions that modify system state or user permissions
   - Admin adding/removing users
   - Role or permission changes
   - Configuration modifications
   - System setting changes

2. **Restricted Data Access** - Any interaction with data labeled as "restricted"
   - Viewing restricted documents
   - Downloading restricted files
   - Querying restricted data
   - Modifying restricted resources

3. **Not Everything Needs Auditing** - Use judgment for routine, non-sensitive operations

## Standard log format

Use this JSON format for all audit logs (written to stdout as single-line JSON):

```json
{
  "timestamp": "2025-11-13T14:23:45.123Z",
  "event_id": "evt_unique123",
  "event_type": "resource.action",
  "severity": "INFO|WARNING|ERROR|CRITICAL",
  "actor": {
    "id": "user-123",
    "type": "user|service_account|api_key|system",
    "name": "email or service name",
    "ip_address": "203.0.113.42"
  },
  "action": {
    "type": "CREATE|READ|UPDATE|DELETE|EXECUTE",
    "outcome": "success|failure|denied",
    "reason": "optional explanation"
  },
  "resource": {
    "id": "unique resource id",
    "type": "resource type",
    "name": "human readable name"
  },
  "context": {
    "environment": "dev|staging|production",
    "service": "service name"
  },
  "metadata": {}
}
```

## Event type naming

Use dot notation for event types:

- `auth.login.success` / `auth.login.failure`
- `user.create` / `user.delete` / `user.update`
- `resource.read` / `resource.update` / `resource.delete`
- `data.restricted.access`
- `infrastructure.change.applied`
- `compliance.access.denied`

## Requirements

- Always use UTC timestamps in ISO 8601 format
- Generate unique `event_id` for each log entry
- Include actor information for all actions
- Log both successes AND failures
- Add relevant metadata (changes made, ticket IDs, data classifications, etc.)
- Write to stdout as single-line JSON for log aggregation systems
