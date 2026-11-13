# Mail-Updater — Software Requirements Specification

**Document status:** Draft 0.4  
**Project:** Mail-Updater  
**Product:** Centralized iRedMail Migration Management System

## 1. Project Purpose

Mail-Updater is a centralized system designed to automate and control the migration of existing iRedMail mail servers to newly provisioned VPS instances running supported operating systems and current iRedMail releases.

Primary objectives:

- Reduce security risks associated with outdated software.
- Minimize human errors during migration.
- Preserve mail data and essential service configurations.
- Provide predictable, repeatable and auditable migration procedures.
- Minimize production downtime.

## 2. Project Scope

Version 1 (v1) of Mail-Updater supports **iRedMail Open Source** only.

### 2.1 In Scope

- Discovery and inventory of existing iRedMail servers.
- Identification of operating system and iRedMail versions.
- Identification of installed services and backend technologies.
- Compatibility assessment.
- Migration planning.
- Registration and validation of manually provisioned target VPS instances.
- Preparation of target operating systems.
- Installation of target iRedMail.
- Migration of mail accounts and mail data.
- Migration of supported configurations.
- Database and directory migration.
- DNS and mail routing transition planning.
- Migration validation.
- Backup and recovery procedures.
- Centralized operation logging and reporting.
- Management of multiple source and target servers.

### 2.2 Out of Scope

Unless explicitly approved later:

- iRedMail Enterprise Edition (v1).
- In-place upgrades of existing production servers.
- Automatic creation or deletion of VMware ESXi virtual machines.
- Automatic provisioning through cloud provider APIs.
- Automatic decommissioning of source servers.
- Uncontrolled production DNS modifications.
- Uncontrolled migration of unsupported third-party applications.
- Replacing iRedMail with another mail server platform.

## 3. Source Environment

The initial infrastructure consists of multiple iRedMail installations hosted as VPS instances on VMware ESXi.

Known characteristics:

- Ubuntu versions: 20.04 through 24.04.
- Different iRedMail releases.
- Some installations date back to 2023.
- Different service configurations and potentially different SQL/LDAP backends.

The exact source compatibility matrix must be established during the research phase.

## 4. Target Environment

Target VPS instances are provisioned manually by the operator.

The target environment must use:

- A supported Ubuntu release.
- A supported and current iRedMail release.
- Compatible database and directory backends.
- Supported mail service components.
- Valid TLS certificates.
- Correct DNS and mail routing configuration.

The target version must be selected according to official iRedMail compatibility and support requirements at migration time.

## 5. System Architecture

Mail-Updater shall use a centralized management architecture.

The central management server is responsible for:

- Web interface and management API.
- Server inventory.
- Migration orchestration.
- Compatibility assessment.
- Migration planning.
- Execution control.
- Logging and reporting.
- Credential management.
- Recovery coordination.

Source and target VPS instances are managed remotely through SSH.

No permanently running Mail-Updater agents are required on source or target servers.

The central management server must not be required to store complete copies of migrated mailboxes.

Whenever technically feasible, large data transfers should occur directly between source and target VPS instances.

## 6. Remote Management and SSH

### FR-01: SSH Connectivity

The system shall establish outbound SSH connections from the central management server to registered source and target VPS instances.

### FR-02: Root Authentication

The system shall connect directly as the root user.

SSH public-key authentication is mandatory for automated operations.

Password-based SSH authentication must not be required for normal automated operation.

### FR-03: SSH Key Management

The system shall support:

- Secure generation or registration of SSH keys.
- Separate key identities for managed servers or migration sessions.
- Secure storage of private keys on the central management server.
- Key assignment to source and target servers.
- Key rotation after successful migration.
- Revocation of obsolete keys.

Private keys must never be copied to source or target servers.

### FR-04: SSH Host Verification

The system shall verify SSH host identities using trusted host keys.

Unknown or changed host keys must trigger a security warning and prevent unattended execution until explicitly resolved.

### FR-05: Root Access Security

Direct root SSH access must be protected by:

- Public-key authentication.
- Disabled password authentication for automated access.
- Network access restrictions where feasible.
- SSH connection timeouts.
- Strict host identity verification.
- Controlled command execution.
- Audit logging.

SSH agent forwarding must not be enabled by default.

Private keys must not be exposed through command-line arguments, environment variables or ordinary logs.

### FR-06: Remote Execution

The system shall execute controlled remote operations and collect:

- Command execution status.
- Exit codes.
- Standard output.
- Standard error.
- Execution timestamps.
- Duration.
- Operation identifiers.

Sensitive values must not appear in ordinary logs.

### FR-07: Connection Failure Handling

The system shall detect:

- SSH connection failures.
- Authentication failures.
- Network interruptions.
- Command timeouts.
- Remote process termination.

Temporary connection failures may be retried according to a defined retry policy.

Operations must be checked for their actual execution state before retrying.

### FR-08: File Transfer

The system shall support secure file transfer using SSH-compatible mechanisms.

The preferred transfer method should avoid storing complete mailbox copies on the central management server.

Transfer integrity must be verified.

### FR-09: SSH Key Rotation

After successful migration and operator acceptance, the system shall support replacement of the SSH key used for migration.

The rotation procedure must:

1. Generate or register a replacement key.
2. Install the replacement public key on the managed server.
3. Verify successful authentication using the replacement key.
4. Revoke the previous public key.
5. Verify that obsolete access is no longer available.
6. Record the rotation result in the audit log.

The old key must not be revoked before successful verification of the replacement access.

Key rotation must not automatically remove unrelated administrative access configured by the operator.

## 7. Target VPS Provisioning

### FR-10: Manual Target Provisioning

The operator is responsible for:

- Creating the target virtual machine in VMware ESXi or another supported virtualization environment.
- Installing the selected Ubuntu release.
- Configuring hostname and network parameters.
- Assigning an IP address.
- Configuring root SSH access.
- Providing sufficient CPU, RAM and storage resources.

Mail-Updater shall not create or delete virtual machines.

### FR-11: Target Registration

Mail-Updater shall allow the operator to register a prepared target VPS.

The registration process must collect connection parameters and information required for migration planning.

### FR-12: Target Readiness Validation

Before installing iRedMail, Mail-Updater shall verify:

- Server connectivity.
- SSH authentication and host identity.
- Operating system and version.
- CPU architecture.
- Available memory.
- Available disk space.
- Network configuration.
- Root administrative access.
- Required package management functionality.
- Absence of conflicting mail services.
- Compatibility with the selected iRedMail release.

Initial target states:

- `TARGET_REGISTERED`
- `TARGET_VALIDATING`
- `TARGET_READY`
- `TARGET_BLOCKED`

## 8. Source Server Inventory

### FR-13: Source Discovery

The system shall identify and record:

- Operating system and version.
- iRedMail version.
- Installed components.
- Database and directory backend.
- Mail domains and accounts.
- Mail storage locations.
- Relevant service configurations.
- Approximate migration data volume.

Source discovery must not modify production configuration or data.

## 9. Compatibility Assessment and Migration Planning

### FR-14: Compatibility Assessment

The system shall evaluate whether the source environment can be migrated to the selected target environment.

Unsupported configurations and migration blockers must be reported before execution.

### FR-15: Migration Planning

The system shall generate an explicit migration plan containing:

- Required operations.
- Dependencies.
- Estimated data volume.
- Compatibility warnings.
- Required backups.
- Validation steps.
- Expected downtime considerations.

Migration procedures must be based on official iRedMail documentation and the requirements of the corresponding source and target releases.

## 10. Target Preparation and iRedMail Installation

### FR-16: Target Preparation

The system shall prepare a validated target VPS for iRedMail installation.

### FR-17: iRedMail Installation

The system shall support installation of the selected target iRedMail release using official installation procedures.

The installed release and component versions must be recorded.

## 11. Data and Configuration Migration

### FR-18: Data Migration

The system shall support migration of applicable:

- Mail accounts and domains.
- Mailbox contents.
- Database records.
- LDAP data, where applicable.
- Aliases and distribution lists.
- DKIM keys.
- Sieve rules.
- Supported user-specific configurations.
- Supported groupware data.

Migration procedures must account for differences between source and target iRedMail releases.

System databases must not be restored indiscriminately from source to target.

Target-specific service credentials and configuration must be preserved.

## 12. Migration Validation

### FR-19: Consistency Verification

The system shall verify:

- Database consistency.
- Account and domain completeness.
- Mailbox data availability.
- File ownership and permissions.
- Service availability.
- Mail delivery functionality.
- TLS and DNS configuration.

Validation must produce a report identifying successful checks, warnings and failures.

## 13. Production Cutover

### FR-20: Controlled Cutover

The system shall support controlled transition from source to target.

The procedure must include:

- Pre-cutover validation.
- Controlled suspension of source-side mail changes where necessary.
- Final data synchronization.
- Final consistency checks.
- Explicit operator approval.
- DNS and mail routing transition.
- Post-cutover validation.

The old server must remain available until migration acceptance is completed.

## 14. Backup and Recovery

### FR-21: Backup Management

The system shall support backup verification and recovery procedures.

### FR-22: Recovery

Interrupted and failed migrations must have documented recovery procedures.

The source server must not be automatically destroyed or decommissioned.

## 15. Security Requirements

- Encrypted remote communication.
- Direct root SSH using public-key authentication.
- SSH host identity verification.
- Secure private key storage.
- Individualized SSH key management.
- Key rotation after successful migration.
- Least exposure of administrative access.
- Network restrictions where feasible.
- Explicit authorization for critical operations.
- Protection against accidental source server destruction.
- Verification of backups before destructive or irreversible operations.
- No automatic source server deletion.
- No automatic production DNS changes without authorization.
- Detailed audit logging.
- Protection of transferred data in transit.
- No exposure of passwords or private keys in ordinary logs.

## 16. Reliability Requirements

- Migration operations must have explicit execution states.
- Interrupted operations must be detectable.
- Recovery procedures must be documented.
- Repeated execution must not blindly duplicate completed operations.
- Failed operations must produce actionable diagnostics.
- Data consistency must be verified before cutover.
- Critical migration steps must support safe interruption and recovery.
- The system must distinguish between completed, failed and partially completed operations.

## 17. Audit and Reporting

Every migration operation must record:

- Migration identifier.
- Source and target identifiers.
- Operation identifier.
- Start and completion timestamps.
- Execution status.
- Exit code, where applicable.
- Diagnostic information.
- Operator approvals.
- SSH key rotation status.

Sensitive information must be excluded from ordinary logs.

## 18. Acceptance Criteria

The initial release will be considered acceptable when:

1. A source server can be inventoried over SSH.
2. A target VPS can be registered and validated.
3. Root SSH host identity can be verified.
4. SSH key rotation can be performed and verified.
5. A compatible migration plan can be generated.
6. Migration blockers are detected before execution.
7. A new iRedMail server can be prepared.
8. Supported data and configurations can be migrated.
9. Data consistency can be verified.
10. A controlled cutover can be performed.
11. The old server remains intact during the acceptance period.
12. All operations are auditable.
13. Recovery procedures are tested in a laboratory environment.

## 19. Open Questions

- Central management server operating system and deployment model.
- **ADR-007:** Version 1 supports iRedMail Open Source only; iRedMail Enterprise Edition is out of scope for v1.
- Supported SQL and LDAP backends.
- Initial source version compatibility matrix.
- SSH key generation and lifecycle policy.
- Centralized secret storage mechanism.
- Mailbox synchronization mechanism.
- Acceptable downtime.
- DNS management automation.
- Retention period for old VPS instances.
- Central management server backup strategy.
- Authentication and authorization model.
- Handling of third-party customizations.

## 20. Requirements Governance

This document is the authoritative source of project requirements.

Implementation must not proceed when a requirement is ambiguous or conflicts with an approved specification.

Changes to this document require explicit approval from the project owner.

The specification must be reviewed and approved before functional development begins.