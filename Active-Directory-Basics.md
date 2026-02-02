# Active Directory Basics – SOC / Blue Team Case Study

## Environment
- Platform: TryHackMe
- Domain: <DOMAIN>.local
- Primary System: Windows Domain Controller (AD DS)
- Access: Domain Administrator (lab-provided)
- Tools Used:
 - Active Directory Users and Computers (ADUC)
 - Group Policy Management Console & Editor
 - Windows PowerShell (administrative)

## Overview
This lab simulates a security-focused review and hardening of a Windows Active Directory environment. Acting as an IT Administrator within a monitored enterprise domain, identity architecture, authentication mechanisms, and Group Policy controls were analyzed, corrected, and validated to reduce attack surface and align with enterprise security best practices.

## Executive Summary (TL;DR)
- Reviewed and restructured an Active Directory domain to improve security, scalability, and policy enforcement.
- Implemented least-privilege delegation, OU-based system separation, and hardened Group Policy configurations.
- Validated administrative and user-level controls using PowerShell and Group Policy testing.
- Analyzed authentication mechanisms, trust relationships, and enterprise risks relevant to SOC and Blue Team operations.

## Objectives
- Identify Active Directory misconfigurations that introduce security risk
- Apply least-privilege principles using OU design and delegation
- Harden authentication and policy enforcement using Group Policy
- Validate controls from both administrative and standard user perspectives
- Understand enterprise-scale AD concepts relevant to SOC investigations

## Key Actions Performed
### Active Directory Structure Hardening
- Reviewed existing OU layout against organizational requirements
- Removed obsolete OUs and user objects after validating business alignment
- Enabled and managed OU deletion protection to prevent accidental damage
- Delegated limited administrative permissions (password resets only) to IT staff

### System Role Separation
- Identified that all computers were placed in the default Computers container
- Created dedicated OUs for:
 - Workstations
 - Servers
- Moved systems based on function to enable role-specific security policies

### Group Policy Hardening
- Reviewed Default Domain Policy and baseline enforcement
- Strengthened password policy by increasing minimum length
- Created and scoped custom GPOs:
 - Restricted Control Panel access for non-IT users
 - Enforced automatic screen lock after inactivity
- Verified GPO distribution via SYSVOL and policy refresh behavior

### Authentication & Trust Analysis
- Analyzed Kerberos authentication flow and ticket lifecycle
- Identified NetNTLM as a legacy protocol with elevated security risk
- Reviewed domain scaling concepts:
 - Domains, Trees, Forests
 - One-way and two-way trust relationships
- Assessed the security impact of Enterprise Admin privileges

## Validation
- Confirmed delegated permissions using PowerShell from non-DC systems
- Verified Group Policy enforcement from standard user accounts
- Ensured correct placement of:
 - 7 systems in the Workstations OU
 - 3 systems in the Servers OU
- Confirmed policy behavior matched intended security controls

## Security Impact
### Risks Reduced
- Excessive privilege exposure through improper delegation
- Weak password policies susceptible to brute-force attacks
- Unauthorized system configuration by non-IT users
- Exposed user sessions due to unattended workstations
- Policy misapplication caused by poor OU design
- Increased lateral movement potential via legacy authentication

## Defensive Value
- Improved domain hygiene and scalability
- Clear separation of high-value assets
- Stronger identity and access controls
- Reduced authentication attack surface
- Improved visibility and monitoring alignment for SOC operations

## Detection & Monitoring Opportunities
- Monitor authentication activity on Domain Controllers
- Alert on NTLM usage where Kerberos should dominate
- Track Group Policy changes and Default Domain Policy modifications
- Monitor SYSVOL replication integrity
- Alert on:
 - Unexpected OU changes
 - Computer object moves between Workstations and Servers
 - Trust relationship or Enterprise Admin membership changes
- Detect authentication downgrade attempts

## Lessons Learned
- Active Directory structure directly impacts security posture
- OU design is foundational to safe Group Policy enforcement
- Delegation must be minimal, scoped, and auditable
- Kerberos is a critical defensive control in Windows environments
- Trust relationships can become invisible attack paths if unmanaged
- Enterprise Admin compromise is a forest-wide incident

## Skills & Concepts Demonstrated
- Active Directory security fundamentals
- Identity and Access Management (IAM)
- Least-privilege enforcement
- Group Policy design and hardening
- Authentication protocol analysis (Kerberos vs. NetNTLM)
- Enterprise domain and trust architecture
- SOC-aligned detection and monitoring thinking

> Note:
> All domain names, usernames, and credentials are sanitized.
> This project is for educational and portfolio demonstration purposes only.
