---
name: data-guardsman
description: >-
  Applies encryption at rest and in transit, key management practices, data
  classification tiers, and handling rules for classified data in code and
  infrastructure. Use when designing APIs, databases, storage, secrets,
  cryptography, TLS, PII/PCI/PHI, logging masking, privacy, or tagging
  DataClassification resources.
disable-model-invocation: true
---

# Data Guardsman

## When to use

When handling **stored or transmitted data**, **secrets**, or **classified information** (code, infra, configs, logs).

## Harness docs first

If the project defines security or coding conventions that conflict, follow project harness docs first.

## Encryption

All data MUST be encrypted both at rest and in transit.

### Encryption in Transit

All network communication must use encryption:

1. **HTTPS/TLS**
   - Use TLS 1.2 or higher (prefer TLS 1.3)
   - Enforce HTTPS for all web traffic
   - Use HSTS headers to prevent downgrade attacks
   - Validate SSL/TLS certificates

2. **API Communication**
   - Use HTTPS for all API calls
   - Never transmit sensitive data over unencrypted connections
   - Validate server certificates

3. **Database Connections**
   - Enable SSL/TLS for database connections
   - Use encrypted connection strings
   - Verify server certificates

4. **Internal Service Communication**
   - Use TLS for service-to-service communication
   - Consider mutual TLS (mTLS) for service authentication

### Encryption at Rest

All stored data must be encrypted:

1. **Database Encryption**
   - Enable encryption at rest for databases (e.g., AWS RDS encryption, Azure SQL TDE)
   - Encrypt backups
   - Use encrypted volumes

2. **File Storage**
   - Enable server-side encryption for object storage (e.g., S3, Azure Blob)
   - Use encryption for file systems
   - Encrypt local storage and caches

3. **Secrets Management**
   - Never store secrets in plaintext
   - Use secret management services (AWS Secrets Manager, Azure Key Vault, HashiCorp Vault)
   - Encrypt environment variables containing sensitive data
   - Use encrypted configuration files

4. **Sensitive Data Fields**
   - Encrypt PII (Personally Identifiable Information)
   - Encrypt financial data
   - Encrypt authentication tokens and session data
   - Encrypt any data marked as "restricted" or "confidential"

### Key Management

1. **Use Strong Keys**
   - Minimum 256-bit keys for symmetric encryption (AES-256)
   - Minimum 2048-bit keys for asymmetric encryption (RSA-2048, prefer RSA-4096)
   - Prefer modern algorithms: AES-256-GCM, ChaCha20-Poly1305

2. **Key Rotation**
   - Rotate encryption keys regularly
   - Implement automatic key rotation where possible
   - Document key rotation procedures

3. **Key Storage**
   - Never hardcode encryption keys
   - Use cloud provider KMS (Key Management Service)
   - Separate key storage from encrypted data
   - Use HSM (Hardware Security Module) for high-security requirements

### Implementation Guidelines

1. **Use Proven Libraries**
   - Use well-established cryptography libraries
   - Don't implement custom encryption algorithms
   - Keep cryptographic libraries up to date

2. **Default to Encrypted**
   - Encryption should be the default, not an option
   - Fail securely if encryption cannot be established

3. **Verify Encryption**
   - Test that encryption is actually enabled
   - Monitor for unencrypted data transmission
   - Audit encryption configurations regularly

### What NOT to Do

- Never roll your own crypto
- Never store encryption keys with the encrypted data
- Never use weak algorithms (MD5, SHA1, DES, 3DES, RC4)
- Never transmit sensitive data over HTTP
- Never store passwords in plaintext (use bcrypt, argon2, or PBKDF2)
- Never commit secrets or keys to version control

## Data classification standard

When writing code that handles data, automatically classify it and apply appropriate controls.

### PUBLIC

Examples: product catalogs, public docs, marketing content  
Controls: None required

### INTERNAL

Examples: employee directories, internal metrics, roadmaps  
Controls: Authentication required

### CONFIDENTIAL

Examples: contracts, financial reports, strategic plans  
Controls: Role-based access, audit logging required

### PII (Personally Identifiable Information)

Examples: emails, phone numbers, addresses, names, IP addresses, device IDs  
Controls:

- Encrypt at rest and in transit
- Mask in logs: `user@example.com` → `u***@example.com`
- Audit all access
- GDPR/privacy compliance required

### PCI (Payment Card Industry)

Examples: credit card numbers, CVV, full PAN  
Controls:

- **NEVER store raw card data**
- Use payment processor tokens only
- PCI-DSS compliance required

### PHI (Protected Health Information)

Examples: medical records, diagnoses, prescriptions, health data  
Controls:

- Encrypt all PHI
- Audit every access
- HIPAA compliance required

### Code requirements

**When handling classified data:**

1. Add comments marking data classification
2. Encrypt sensitive fields before storage
3. Mask sensitive data in logs (use IDs, not values)
4. Add audit logging for PII/PCI/PHI access
5. Apply data minimization (only collect what's needed)
6. Tag infrastructure resources with `DataClassification` tag

**Red flags to catch:**

- Logging PII/PCI/PHI values directly
- Storing credit card data
- Missing encryption for PII/PHI
- Sending sensitive data to analytics without anonymization
- Missing audit logs for sensitive operations
