# Active Directory Basics – SOC / Blue Team Lab

## Room Metadata
- **Platform:** TryHackMe
- **Room:** Active Directory Basics
- **Link:** <https://tryhackme.com/room/winadbasics>
- **Difficulty:** Easy
- **Date Completed:** In Progress
- **Primary Focus Areas:** Active Directory
- **Role Perspective:** IT Administrator

---

## Executive Summary (TL;DR)
- Reviewed and restructured a Windows Active Directory domain to align with updated organizational requirements.
- Removed obsolete Organizational Units (OUs), enforced least-privilege delegation, and validated password reset controls.
- Demonstrated secure identity and access management practices relevant to SOC and Blue Team operations.
- Validated changes using both administrative and standard user perspectives.

---

## 1. Objective
- Understand how Windows domains centralize identity, authentication, and policy enforcement as organizations scale, and how misconfigurations can introduce security risk.

---

## 2. Scenario (SOC / Real-World Context)
Acting as the newly appointed IT administrator at THM Inc., tasked with reviewing and maintaining a growing Windows domain environment (THM.local).

---

## 3. Skills & Tools Demonstrated

### Skills
- Reviewing Active Directory domain structure for security and operational accuracy
- Mapping organizational growth to authentication and authorization requirements
- Identifying when centralized identity administration is required
- Applying least-privilege principles through delegation

### Tools / Technologies
- Browser-based Windows Domain Controller access
- TryHackMe AttackBox (environment support)

---

## 4. Environment Overview

- **Role:** Domain Administrator (pre-configured lab credentials)
- **Domain:** THM.local
- **Primary System:** Windows Domain Controller (AD DS)
- **Access Level:** Domain Administrator (lab-provided)
- **Access Method:** Browser-based Windows session
- **Supporting System:** TryHackMe AttackBox (Linux)

---

## 5. Environment & Concept Familiarization
### Active Directory & Windows Domain Concepts Reviewed

- **Active Directory (AD):** Centralized identity and access management service for Windows enterprise environments.
- **Windows Domain:** Logical grouping of users and computers managed through AD to enable centralized authentication and policy enforcement.
- **Domain Controller (DC):** Server running Active Directory Domain Services, responsible for authentication, authorization, and directory replication.
- **Domain Benefits:** Centralized identity management and centralized security policy enforcement.

### Active Directory Objects Overview
The following Active Directory objects were reviewed to understand how enterprise Windows domains are structured and managed:

- **User Accounts:** Represent individual identities used for authentication and authorization within the domain.
- **Computer Accounts:** Represent machines joined to the domain. These accounts automatically append a `$` character to the machine name (e.g., `TOM-PC$`).
- **Security Groups:** Logical collections of users and/or computers used to assign permissions to resources.
- **Organizational Units (OUs):** Containers used to logically organize objects and apply Group Policies.

**Key Distinctions:**
- A user or computer object can only belong to **one OU** at a time.
- A user or computer object can belong to **multiple security groups**.
- OUs are typically used to reflect **organizational roles or departments**.
- Security groups are typically used to control **access to resources**.

These concepts form the foundation for enforcing identity-based security controls and investigating authentication-related incidents in enterprise environments.

---

## 6. Methodology & Task Walkthrough
### 6.1 Identification
#### Task 2:
- Initial identification focused on understanding the scope of the environment and the centralized role of the Domain Controller in managing users, computers, and authentication for the THM.local domain.

#### Task 3:
- Upon accessing the Domain Controller, the existing Active Directory structure was reviewed using Active Directory Users and Computers (ADUC).

An Organizational Unit (OU) named `THM` was identified, containing the following child OUs:
- IT
- Management
- Marketing
- Research and Development
- Sales

#### Task 4:
As the new domain administrator, the existing Active Directory structure was reviewed to align it with the updated organizational chart.

The following discrepancies were identified:
- Organizational Units present in the domain but not in the official org chart: Research and Development, Students
- A user account within Research and Development that no longer aligned with business requirements, and 2 user accounts within Sales

---

### 6.2 Data Collection
#### Task 2:
- Reviewed domain architecture concepts, including where credentials are stored and which systems are responsible for authentication services.

#### Task 3:
- Active Directory Users and Computers was launched directly from the Domain Controller using the Windows search interface.

The following actions were explored within various OUs:
- Creating and deleting objects
- Modifying user attributes
- Moving objects between OUs
- Resetting passwords
- Disabling user accounts
- Adding users to security groups
- A new Organizational Unit named `Students` was created under the `THM` OU.  
The option to protect the OU from accidental deletion was left enabled to prevent unintentional administrative errors, not mandatory in this situation.

#### Task 4:
- Active Directory Users and Computers (ADUC) was used on the Domain Controller to inspect and manage OUs.
- Attempting to delete the Research and Development OU initially failed due to accidental deletion protection being enabled.

To proceed:
- Advanced Features were enabled from the ADUC View menu
- OU properties were accessed
- “Protect object from accidental deletion” was disabled
  
- The OU was successfully deleted

The same process was repeated for the Students OU.

---

### 6.3 Analysis
#### Task 2:
- Manual, per-host user management does not scale and increases risk of inconsistent security controls.
- Centralized credential storage in Active Directory reduces administrative overhead and improves visibility.
- Domain Controllers represent high-value assets; compromise would impact the entire domain.

#### Task 3:
- Organizational Units provide a structured way to apply Group Policies consistently based on business roles or departments.

Using OUs for departmental separation enables:
- Centralized policy enforcement
- Reduced administrative overhead
- Clear separation of duties

While OUs define administrative and policy boundaries, security groups are more appropriate for managing access to shared resources, as users can be members of multiple groups simultaneously.

#### Task 4:
- OU deletion protection is a safeguard against accidental administrative actions. While beneficial in production environments, it must be temporarily removed when legitimate structural changes are required.
- Delegation changes should be logged and monitored, as improper delegation can enable privilege escalation.
  
This reinforces:
- Change control awareness
- The importance of validating business justification before modifying AD structure

---

### 6.4 Containment / Hardening / Recovery
#### Task 4:
- To reduce administrative overhead and follow least-privilege principles, password reset permissions were delegated to the IT department.
- Using the Delegation of Control Wizard, the following task was delegated on remaining OUs:
 - Reset user passwords
 - Force password change at next logon

Other delegation options (account creation, deletion, group management) were reviewed but not applied.

---

## 7. Validation & Results
#### Task 2:
- Confirmed understanding that credentials in a Windows domain are stored in Active Directory.
- Verified that Domain Controllers are responsible for running Active Directory services and handling authentication.

#### Task 3:
- The newly created `Students` Organizational Unit was successfully visible within the `THM` OU hierarchy in Active Directory Users and Computers.

- Basic administrative actions (create, modify, move, disable) were confirmed to be available for objects within the OU.

#### Task 4:
To validate delegated permissions:
- Logged in as an IT user (<USERNAME>) on a non-domain controller system
- Confirmed ADUC was unavailable, as expected
- Used PowerShell to perform delegated actions
- Password reset command executed:
  ``` Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose ```
- After resolving a password policy violation, the operation completed successfully.
- Forced password change at next logon:
  ``` Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose ```
- Both actions returned successful verbose output.
- User Perspective Validation:
  - Logged in as the affected user with the newly set credentials.
  - On successful login: A text file named flag.txt was present on the desktop
  - The flag was retrieved successfully

---

## 8. SOC Notes / Sample Ticket
TBD

**Summary:**  
TBD

**Risks Mitigated:**
TBD

**Recommendations:**
TBD

---

## 9. Lessons Learned
#### Task 2:
- Windows domains are essential for managing identity and access at scale.
- Centralized authentication improves both security enforcement and incident response.
- Domain Controllers must be protected and monitored due to their critical role.

#### Task 4:
- OU protection mechanisms prevent accidental damage but must be consciously managed
- Delegation allows operational tasks to be distributed without over-privileging users
- PowerShell enables delegated AD management even from non-domain controllers
- Password policies are enforced consistently across delegated operations

---

## 10. Detection & Monitoring Opportunities
- Monitor authentication events on Domain Controllers (e.g., Event IDs 4624, 4768).
- Alert on excessive authentication failures or unusual logon patterns.

---

## 11. Certification & Skill Mapping

### SOC / Blue Team Concepts
- Identity and Access Management (IAM)
- Least privilege enforcement
- Administrative delegation
- Authentication monitoring

### CompTIA Mapping
- Identity and Access Management (IAM)
- Centralized authentication and authorization
- Enterprise security architecture

---

## 12. Optional Enhancements
TBD

---

**Note:** All usernames, domain names, IP addresses, and credentials in this repository have been sanitized or replaced with placeholders.  
> This content is for educational and portfolio demonstration purposes only.
