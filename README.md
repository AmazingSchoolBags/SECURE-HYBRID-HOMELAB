# 🏠 Secure Hybrid Homelab - Infrastructure & Cybersécurité

## 🎯 Vision du Projet
Ce laboratoire est une réplique miniature d'une infrastructure d'entreprise. Il me permet de tester des scénarios d'attaque/défense, de gérer des environnements de virtualisation critiques et de maîtriser l'hybridation avec le Cloud Microsoft.

---

## 🏗️ Architecture Réseau & Sécurité
* **Cœur de réseau :** Firewall **Stormshield SN210** (Filtrage ASQ & IPS).
* **Accès VPN :** VPN SSL (Port 17433) avec restriction Géo-IP (France) et liste blanche sur l'IP publique de l'entreprise d'alternance.
* **Segmentation (VLANs) :**
| VLAN | Nom | Usage |
| :--- | :--- | :--- |
| **10** | SIEGE | Administration & Management |
| **20** | WIFI | Accès sans-fil (Settat) |
| **30** | INVITE | Accès internet isolé |
| **40** | USERS | Postes de travail & Terminaux |
| **50** | LAB | Environnement de test & Machines vulnérables |

## 🖥️ Virtualisation & Haute Disponibilité
* **Hyperviseur :** Cluster de 2 nœuds **Proxmox VE** avec Quorum.
* **Stockage :** Baie de stockage partagée pour la migration à chaud (vMotion-like) et la HA.
* **Monitoring :** Serveur physique dédié pour **Proxmox Datacenter Manager** (indépendance vis-à-vis du cluster).
* **Supervision :** Instance **Zabbix** (VM Ubuntu) surveillant SNMP (Firewall, Switchs) et les ressources systèmes.

## ☁️ Modern Workplace & Cloud (Hybride)
* **Identité :** Windows Server 2025 local synchronisé avec **Azure AD (Entra ID Connect)**.
* **Endpoint Management :** Enrôlement hybride (VMs + Physiques) dans **Microsoft Intune**.
* **Sécurité :** Authentification MFA via **tokens physiques Thales**.
* **Données :** Migration complète des File Servers locaux vers **SharePoint Online**.

---

## 📊 Schéma d'Architecture
<img width="2752" height="1536" alt="Schéma Architecture Mohamed CT" src="https://github.com/user-attachments/assets/67231677-9564-4209-92a1-d5dc717eddf5" />

<details>
<summary>🔎 Cliquez ici pour voir le schéma en mode texte (Logique réseau)</summary>

```text
                                 ┌───────────────┐
                                 │   INTERNET    │
                                 └───────┬───────┘
                                         │
                                 ┌───────┴───────┐
                                 │ Box Internet  │ (Mode Bridge/DMZ)
                                 └───────┬───────┘
                                         │
                          WAN IP : 192.168.1.200 (Port 17433)
                                         │
                         ┌───────────────┴───────────────┐
                         │      STORMSHIELD SN210        │
                         │   (VPN SSL / Géo-IP France)   │
                         └───────┬───────────────┬───────┘
                                 │               │
                 ┌───────────────┘               └───────────────┐
                 │                                               │
      [ LAN (Trunk VLANs) ]                            [ DMZ (10.0.1.0/24) ]
                 │                                               │
      ┌──────────┴──────────┐                         ┌──────────┴──────────┐
      │     SWITCH LAN      │                         │     SWITCH DMZ      │
      │     HP 2620-24      │                         │     HPE 1920S       │
      └──────────┬──────────┘                         └──────────┬──────────┘
                 │                                               │
    ┌──────┬─────┴┬──────┬──────┐                  ┌─────────────┼─────────────┐
    │      │      │      │      │                  │             │             │
 VLAN 10 VLAN 20 VLAN 30 VLAN 40 VLAN 50     ┌─────┴─────┐ ┌─────┴─────┐ ┌─────┴─────┐
 SIEGE   WIFI   INVITE  USERS   LAB        │ NODE PVE 1│ │ NODE PVE 2│ │  PDM (Phy)│
    │      │      │      │      │          └─────┬─────┘ └─────┬─────┘ └───────────┘
 Admin  AP/WLC Guests   PCs    VMs               │             │
                                           ┌─────┴─────────────┴─────┐
                                           │   STOCKAGE PARTAGÉ HA   │
                                           └─────────────────────────┘
