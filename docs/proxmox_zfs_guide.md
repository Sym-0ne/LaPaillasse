# Proxmox 

---

# 1. Introduction
Ce guide fournit une documentation complète et détaillée sur l'utilisation de **Proxmox VE**. Il explique les concepts, le fonctionnement, les avantages, et propose des instructions pratiques pour la mise en place et la gestion de l’infrastructure.

---

# 2. Concepts fondamentaux

## 2.1 Proxmox VE
Proxmox VE est une solution open-source de virtualisation, permettant de gérer :
- **Machines virtuelles (VM)** via KVM.
- **Containers Linux (LXC)** pour des services légers.
- **Clusters multi-nœuds** pour la haute disponibilité.
- **Stockages avancés**, incluant ZFS, Ceph, NFS, iSCSI.

**Objectif pédagogique** : Proxmox permet de comprendre le fonctionnement d’une infrastructure virtuelle complète, de l’hébergement de services à la gestion des ressources réseau et stockage.


# 5. Installation de Proxmox VE

## 5.1 Téléchargement et préparation
1. Télécharger l’ISO officiel depuis [proxmox.com](https://www.proxmox.com/en/downloads).
2. Créer une clé USB bootable.
3. Installer sur le serveur physique.

## 5.2 Configuration initiale
- Définir l’adresse IP fixe.
- Configurer le mot de passe root.
- Vérifier l’accès à l’interface Web (`https://<ip-du-serveur>:8006`).

---

# 8. Surveillance et maintenance
- Vérifier l’état du pool : `zpool status`
- Surveiller les alertes SMART des disques.
- Faire des snapshots réguliers avant les TP critiques.
- Prévoir une sauvegarde externe avec **Proxmox Backup Server** pour plus de sécurité.

---

# Annexes
- Commandes utiles :
```bash
zpool list
zpool status
```
- Liens :
  - [Documentation Proxmox VE](https://pve.proxmox.com/pve-docs/)

