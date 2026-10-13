
# Active Directory — Domain Deployment and Management

## 12. Additional Domain Controller (ADC)

An Additional Domain Controller (ADC) provides redundancy and distributes directory services within an existing domain.

### Prerequisites

- Configure a static IP address on the new server.
- Configure the Preferred DNS Server to use an existing internal domain DNS server.
- Ensure network connectivity with an existing Domain Controller.
- Use an account with sufficient domain privileges.

### Installation and Configuration

1. Sign in to the new server using an account with appropriate administrative privileges.
2. Verify the server name and network configuration.
3. Open **Server Manager → Manage → Add Roles and Features**.
4. Select **Role-based or feature-based installation**.
5. Select the destination server.
6. Select **Active Directory Domain Services (AD DS)**.
7. Click **Add Features** and complete the installation.
8. In Server Manager, select **Promote this server to a domain controller**.
9. Select **Add a domain controller to an existing domain**.
10. Enter the domain name and authorized credentials.
11. Configure DNS and Global Catalog options as required.
12. Set the **Directory Services Restore Mode (DSRM)** password.
13. Run the prerequisite checks and select **Install**.
14. After the server restarts, verify DNS and Active Directory replication.

> **Note:** Installing the AD DS role alone does not make the server a Domain Controller. The promotion process is also required.

---

## 13. Active Directory Users and Computers

Use this console to manage domain users, groups, computers, and organizational units (OUs).

**GUI:** `Server Manager → Tools → Active Directory Users and Computers`

**Run command:**

```cmd
dsa.msc
```

---

## 14. Domain Controllers OU

**Path:**

`Active Directory Users and Computers → Domain → Domain Controllers`

The default Domain Controllers OU contains the computer accounts of Domain Controllers. Group Policies linked to this OU can apply settings to those servers.

---

## 15. Computer Accounts

Every domain-joined computer has a computer account in Active Directory.

Computer accounts support computer authentication, identification, and centralized management.

---

## 16. Verify Shares and AD Management Tools

To list the server's shared resources, run:

```cmd
net share
```

To open Active Directory Users and Computers, run:

```cmd
dsa.msc
```

Verify that the expected shares are present and that the management console opens successfully.

> **Note:** These checks alone do not confirm the overall health of Active Directory.

---

## 17. Access SYSVOL and NETLOGON

Open Run or File Explorer and enter:

```text
\\<DomainName>\SYSVOL
\\<DomainName>\NETLOGON
```

Replace `<DomainName>` with the DNS name of your domain.

- **SYSVOL:** Stores Group Policy files and domain-related scripts.
- **NETLOGON:** Provides access to logon scripts and related files.

---

## 18. Active Directory Backup

System State backups help recover Active Directory after server failures or accidental changes.

- Use **Windows Server Backup** or a compatible backup solution.
- Store backups securely, preferably separate from the source server.
- Regularly verify that backups can be restored.

> **Note:** A backup is only useful if the recovery process has been validated.

---

## 19. Create a Child Domain

A Child Domain is a domain created beneath an existing parent domain in the same forest.

**Example:**

```text
example.com
└── child.example.com
```

### Deployment Steps

1. Configure a static IP address and the appropriate DNS settings on the new server.
2. Set the server name and verify connectivity with the parent domain.
3. Install the **Active Directory Domain Services (AD DS)** role.
4. Select **Promote this server to a domain controller**.
5. Select **Add a new domain to an existing forest**.
6. Under **Domain Type**, select **Child Domain**.
7. Specify the parent domain.
8. Enter the child domain name, such as `child`.
9. Provide credentials authorized to create the domain.
10. Configure DNS, set the DSRM password, and review the remaining options.
11. Run the prerequisite checks and start the installation.
12. After the restart, verify the new domain and DNS configuration.

**Required privileges:** Membership in Enterprise Admins is typically required for creating a new domain in an existing forest.

---

## 20. Create a Tree Domain

A Tree Domain is a domain with a different DNS namespace that is added to an existing forest.

**Example:**

```text
Forest: example.com
├── example.com
└── example.net
```

### Deployment Steps

1. Configure a static IP address and DNS settings appropriate for the existing forest.
2. Install the **Active Directory Domain Services (AD DS)** role.
3. Select **Promote this server to a domain controller**.
4. Select **Add a new domain to an existing forest**.
5. Under **Domain Type**, select **Tree Domain**.
6. Specify the existing forest root domain.
7. Enter the full DNS name of the new tree domain, such as `example.net`.
8. Provide credentials authorized to create the domain.
9. Configure DNS, set the DSRM password, and review the remaining options.
10. Run the prerequisite checks and start the installation.
11. After the restart, verify the forest structure and DNS configuration.

**Required privileges:** Membership in Enterprise Admins is typically required for creating a new tree domain in an existing forest.

---

## 21. View the Forest Structure

To view the domain structure:

1. Open **Active Directory Domains and Trusts**.
2. Expand the domain tree.
3. Open the domain properties to review available trust and configuration information.

A forest is the top-level logical structure in Active Directory. Domains in the same forest share a schema and a global catalog infrastructure.

---

## 22. Join a Client Computer to a Domain

### Prerequisites

- Network connectivity to a Domain Controller.
- DNS configured to use an appropriate internal domain DNS server.
- Local administrative privileges on the client.
- Credentials authorized to join the computer to the domain.

### Steps

1. Verify the client's IP and DNS settings.
2. Open the computer name and domain settings.
3. Select the option to change the computer name or domain membership.
4. Select **Domain**.
5. Enter the domain name, such as `example.com`.
6. Enter authorized domain credentials when prompted.
7. After receiving the success message, restart the computer.
8. Sign in using a domain account, for example:

   ```text
   EXAMPLE\username
   ```

9. Open Active Directory Users and Computers and verify the computer account.

> **Note:** The settings path varies by Windows version. Windows Home editions cannot directly join a traditional Active Directory Domain.

---

## 23. Demote a Domain Controller

Demotion removes the Domain Controller role from a server.

### Steps

1. Verify the health of the remaining Domain Controllers and replication.
2. Check FSMO roles, DNS, Global Catalog settings, and dependent services.
3. In Server Manager, open the AD DS section and select **More**.
4. Select **Demote this domain controller**.
5. Review the warnings and provide the required credentials.
6. Do not select the last-domain-controller option unless you intend to remove the domain.
7. Set the local Administrator password for use after demotion.
8. Complete the wizard and restart the server when prompted.
9. Remove the AD DS role if it is no longer needed.

> **Warning:** Do not perform a forced demotion without assessing its impact on replication and Active Directory metadata.

---

## 24. Last Domain Controller in the Domain

The last-domain-controller option is used when demoting the final Domain Controller in a domain.

- Do not remove the last Domain Controller unless domain removal is intended.
- Removing the final Domain Controller removes that domain from the forest.
- If it is the only domain in the forest, removing it eliminates the forest.
- Review dependencies, backups, DNS, and administrative access before proceeding.

**Required privileges:** Membership in Enterprise Admins is typically required to remove the last Domain Controller of a domain in an existing forest.
