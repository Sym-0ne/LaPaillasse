# Configuration des Switchs HP A5500 (Comware)

Cette documentation décrit pas à pas la configuration initiale d’un switch HP A5500 sous Comware.  
Elle couvre les points suivants :  

- Connexion au switch via le port console depuis Linux Mint  
- Réinitialisation de la configuration (factory reset)  
- Configuration d’un stack IRF (Intelligent Resilient Framework)  
- Mise en place d’un VLAN de management (VLAN 120)  
- Activation de l’accès SSH pour l’administration  

---

## 🔌 Connexion au switch via port console (Linux Mint)

### Matériel requis
- Câble console (RJ45-DB9 ou adaptateur USB-RJ45 selon le modèle)
- PC sous Linux Mint avec droits administrateur

### Étapes
1. Brancher le câble console entre le PC et le switch.  
2. Identifier le port série :
   ```bash
   dmesg | grep ttyUSB
