# hybrid-security-lab

Personal infrastructure and security lab built on AWS, covering identity management, secure communications, network security and endpoint protection.

Designed to simulate a real-world hybrid environment combining Windows Server, Linux, and cloud infrastructure — with security mechanisms applied at each layer.

---

## Environment

| Instance | OS | Role |
|---|---|---|
| Win-Server | Windows Server 2022 | Domain Controller (AD DS) |
| Win-Client | Windows Server 2022 | Workstation joined to domain |
| Linux-Server | Ubuntu 22.04 | File server (NFS), DNS client, VPN server, HTTPS |
| Linux-Client | Ubuntu 22.04 | NFS client, connectivity testing |

All instances provisioned on **AWS EC2** within a private VPC.

---

## Technologies Used

- **Cloud:** AWS EC2, VPC, Security Groups
- **Identity & Access:** Active Directory, Group Policy (GPO)
- **Network Services:** DNS, NFS, DHCP
- **Secure Communications:** WireGuard VPN, HTTPS, SSL/TLS (self-signed certificate)
- **Endpoint Security:** Bitdefender GravityZone (corporate console + agent)
- **Automation:** crontab (scheduled backup)
- **Simulation/Diagramming:** Cisco Packet Tracer
- **OS:** Windows Server 2022, Ubuntu 22.04

---

## Architecture

```
                        AWS VPC
        ┌───────────────────────────────────────┐
        │                                       │
        │   ┌─────────────┐   ┌─────────────┐  │
        │   │  Win-Server │   │  Win-Client │  │
        │   │  AD DS/DNS  │◄──│  Domain     │  │
        │   │  GravityZone│   │  Member     │  │
        │   └─────────────┘   └─────────────┘  │
        │                                       │
        │   ┌─────────────┐   ┌─────────────┐  │
        │   │ Linux-Server│   │Linux-Client │  │
        │   │ NFS/VPN/HTTPS──►│ NFS mount   │  │
        │   │ crontab     │   │ DNS client  │  │
        │   └─────────────┘   └─────────────┘  │
        │                                       │
        └───────────────────────────────────────┘
                         │
                    WireGuard VPN
                         │
                   Personal endpoint
```

---

## Lab Stages

### [Stage 1 — Environment Setup](./stage-1-setup/)
AWS account provisioning, VPC configuration, OS licensing and network topology diagram built in Cisco Packet Tracer for visual documentation of the environment.

### [Stage 2 — Access Control](./stage-2-access-control/)
Active Directory deployment with domain controller, GPO-enforced restrictions, NFS file sharing between Linux instances, DNS integration and automated backup via crontab.

### [Stage 3 — Secure Communications](./stage-3-secure-comms/)
WireGuard VPN server configured on Linux with external endpoint connection, HTTPS web server with self-signed SSL certificate, and Bitdefender GravityZone deployed in centralized management mode.

### [Stage 4 — Layer 2 Security](./stage-4-network-security/) *(in progress)*
VLAN segmentation and Layer 2 attack simulations in Cisco Packet Tracer, including attack demonstration and active defense mechanisms.

---

## Security Mechanisms by Layer

| Layer | Mechanism | Implementation |
|---|---|---|
| Identity | Authentication & authorization | Active Directory + GPO |
| Network | Traffic segmentation | VLAN (Packet Tracer) |
| Network | Secure remote access | WireGuard VPN |
| Transport | Encrypted communication | HTTPS + SSL/TLS |
| Endpoint | Threat protection | Bitdefender GravityZone |
| Data | Availability | Automated backup (crontab) |

---

## Related Projects

- [wazuh-soc-lab](https://github.com/l-shiroma/wazuh-soc-lab) — SOC homelab with Wazuh SIEM, Sysmon telemetry and MITRE ATT&CK detection scenarios
