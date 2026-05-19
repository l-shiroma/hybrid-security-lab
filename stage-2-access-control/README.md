# Stage 2 — Access Control

Implementation of identity management and file sharing services across a hybrid Windows/Linux environment hosted on AWS.

---

## Objectives

- Deploy a Windows Server domain controller with Active Directory
- Enforce access restrictions via Group Policy (GPO)
- Configure NFS file sharing between Linux instances
- Integrate Linux clients with Windows DNS
- Automate backup with crontab

---

## Architecture

```
Win-Server (AD DS + DNS)
    │
    ├── authenticates ──► Win-Client (domain member, GPO applied)
    │
    └── DNS resolution ──► Linux-Server
                               │
                               ├── NFS share ──► Linux-Client
                               └── crontab backup ──► /backup
```

---

## Implementation

### Active Directory & Domain Controller

Windows Server configured as domain controller with AD DS role.

```powershell
# Install AD DS role
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Promote to domain controller
Install-ADDSForest -DomainName "lab.local"
```

Users created and joined to the domain. Win-Client enrolled as domain workstation.

### Group Policy (GPO)

Restrictive policy applied to domain workstations demonstrating access control enforcement:
- Control Panel access blocked
- Desktop restrictions applied via GPO linked to domain OU

```powershell
# Create and link GPO
New-GPO -Name "Workstation-Restrictions" | New-GPLink -Target "OU=Workstations,DC=lab,DC=local"
```

### NFS File Server (Linux-Server)

```bash
# Install NFS server
sudo apt install nfs-kernel-server -y

# Create shared directory
sudo mkdir -p /srv/nfs/shared
sudo chown nobody:nogroup /srv/nfs/shared

# Configure exports
echo "/srv/nfs/shared <linux-client-ip>(rw,sync,no_subtree_check)" | sudo tee -a /etc/exports

# Apply configuration
sudo exportfs -a
sudo systemctl restart nfs-kernel-server
```

### NFS Mount (Linux-Client)

```bash
# Install NFS client
sudo apt install nfs-common -y

# Mount shared folder
sudo mount <linux-server-ip>:/srv/nfs/shared /mnt/shared

# Verify mount
df -h | grep shared
```

### DNS Integration (Linux instances)

Linux instances configured to use Windows Server as their DNS resolver, enabling name resolution within the VPC. All four instances operate on the same AWS VPC and can communicate with each other directly.

```bash
# /etc/resolv.conf
nameserver <win-server-ip>
```

### Automated Backup (crontab)

Daily backup of `/home` directory scheduled via crontab on Linux-Server.

```bash
# Crontab entry (runs daily at 2:00 AM)
0 2 * * * tar -czf /backup/home_$(date +\%Y\%m\%d).tar.gz /home/
```

```bash
# Verify backup directory
ls -lh /backup/
```

---

## Key Decisions

**Why AWS over local VMs?**
AWS provides a persistent, accessible environment without dependency on a single physical machine. All team members can access their own isolated environment independently.

**Why NFS for file sharing?**
NFS is the standard protocol for Linux-to-Linux file sharing in enterprise environments and is directly applicable to real-world sysadmin scenarios.

**Why point Linux DNS to Windows Server?**
Centralizing DNS on the Windows Server keeps name resolution consistent across all instances in the VPC, which is the standard approach in environments where a Windows domain controller is already handling DNS.

---

## Screenshots

> Add screenshots to the `screenshots/` folder and reference them here.

| Description | File |
|---|---|
| AWS console showing 4 instances | `screenshots/aws-instances.png` |
| Active Directory users | `screenshots/ad-users.png` |
| Win-Client logged in with GPO restriction | `screenshots/gpo-restriction.png` |
| NFS mount on Linux-Client (df output) | `screenshots/nfs-mount.png` |
| Backup folder with crontab output | `screenshots/backup-folder.png` |
