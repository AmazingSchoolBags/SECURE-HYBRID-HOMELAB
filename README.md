# 🏠 Secure Hybrid Homelab - Infrastructure & Cybersécurité

## 🎯 Vision du Projet
Ce laboratoire est une réplique miniature d'une infrastructure d'entreprise. Il me permet de tester des scénarios d'attaque/défense, de gérer des environnements de virtualisation critiques et de maîtriser l'hybridation avec le Cloud Microsoft.

## 🏗️ Architecture Réseau & Sécurité
* **Cœur de réseau :** Firewall **Stormshield SN210** assurant le filtrage et l'inspection de flux.
* **Accès VPN :** VPN SSL sur port personnalisé (**17433**) avec restriction par Géo-IP (France) et filtrage par IP publique source (IP entreprise).
* **Segmentation :** 5 VLANs isolés (Siège, WiFi, Invités, Users, Lab) gérés via un switch **HP 2620-24** et routage inter-VLAN.

## 🖥️ Virtualisation & Haute Disponibilité
* **Hyperviseur :** Cluster de 2 nœuds **Proxmox VE** avec quorum et stockage partagé pour la **Haute Disponibilité (HA)** des VMs.
* **Gestion :** Serveur physique dédié pour **Proxmox Datacenter Manager** afin de garantir une visibilité constante.
* **Services Critiques :**
    * **Identité :** Windows Server 2025 local synchronisé avec **Azure AD (Entra ID Connect)**.
    * **Supervision :** Instance **Zabbix** monitorant le SNMP des switchs, le firewall et les ressources serveurs.

## ☁️ Modern Workplace & Cloud
* **Tenant M365 Dev :** Domaine personnalisé (`amen.fr`) avec configuration complète des enregistrements DNS (MX, TXT, SPF).
* **Endpoint Management :** Enrôlement de machines physiques et virtuelles dans **Microsoft Intune**.
* **Sécurité Forte :** Déploiement du MFA via **tokens physiques Thales**.
* **Migration :** Transition réussie de serveurs de fichiers locaux vers **SharePoint Online**.

### 🏗️ Architecture du Homelab

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
                                                         │
                                           ┌─────────────┼─────────────┐
                                           │  SERVICES CRITIQUES (VMs) │
                                           ├───────────────────────────┤
                                           │ 🔹 Windows Server 2025    │
                                           │ 🔹 Zabbix (Ubuntu Server) │
                                           │ 🔹 Proxmox Backup Server  │
                                           └───────────────────────────┘

 ## 📊 Schéma d'Architecture 
 <img width="2752" height="1536" alt="image" src="https://github.com/user-attachments/assets/67231677-9564-4209-92a1-d5dc717eddf5" />

 ## 🎯 Objectifs Techniques
- Sécurisation d’infrastructure hybride (On-prem + Azure)
- Simulation d’attaques (lateral movement, brute force, privilege escalation)
- Supervision & détection d’anomalies
- Automatisation & Infrastructure as Code
- Résilience & reprise après incident


