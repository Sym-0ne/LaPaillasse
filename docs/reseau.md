# Configuration des Switchs HP A5500 (Coeur de Réseau)

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
2. Identifier le port série :<br>
   ```bash 
   dmesg | grep ttyUSB
   ```
3. Installer un client console (exemple : minicom) : sudo apt install minicom
4. Lancer la session console :sudo minicom -D /dev/ttyUSB0 -b 9600

⚙️ Paramètres de connexion série :

- Vitesse : 9600 bauds
- Data bits : 8
- Parité : aucune
- Stop bits : 1

## 🔄 Réinitialisation de la configuration (Factory Reset)

1. Redémarrer le switch.
2. Interrompre le boot avec Ctrl+B.
3. Entrer le mot de passe bootrom (souvent vide ou password).
4. Choisir l’option Skip current configuration.
5. Une fois démarré, supprimer la configuration existante :
    ```<HP> reset saved-configuration
    ```

6. Redémarrer :
    ```<HP> reboot
    ```

👉 Le switch redémarre avec la configuration d’usine.

## 🖇 Configuration d’un stack IRF (Intelligent Resilient Framework)

1. Vérifier que les deux switchs ont la même version logicielle.
2. Configurer l’ID IRF sur chaque switch :
   ```<HP> system-view
   ```
   ```[HP] irf member 1 renumber 1   ← premier switch
   ```
   ```[HP] irf member 1 renumber 2   ← deuxième switch
   ```

3. Sauvegarder et redémarrer :

    ```[HP] save
    ```

4. Configurer les ports IRF :<br>
   ```[HP] system-view
   ```
   ```[HP] irf-port 1/1
   ```
   ```[HP-irf-port1/1] port group interface Ten-GigabitEthernet1/0/49
   ``` 
   ```[HP-irf-port1/1] quit
   ```
   ```[HP] irf-port 2/1
   ```
   ```[HP-irf-port2/1] port group interface Ten-GigabitEthernet2/0/49
   ```
   ```[HP-irf-port2/1] quit
   ```

5. Activer la configuration IRF et redémarrer :
   ```[HP] irf-port-configuration active
   ```
   ```[HP] save
   ```
   ```[HP] reboot
   ```
## 🌐 Étape 4 : Création d’un VLAN de management (VLAN 120)

1. Créer le VLAN 120 :<br>
   ```[HP] vlan 120
   ```
   ```[HP-vlan120] quit
   ```
2. Créer l’interface VLAN et attribuer une adresse IP libre :<br>
   ```[HP] interface Vlan-interface 120
   ```
   ```[HP-Vlan-interface120] ip address 192.168.120.10 255.255.255.0
   ```
   ```[HP-Vlan-interface120] quit
   ```
3. Associer un port physique au VLAN 120 :<br>
   ```[HP] interface GigabitEthernet1/0/1
   ```
   ```[HP-GigabitEthernet1/0/1] port link-type access
   ```
   ```[HP-GigabitEthernet1/0/1] port access vlan 120
   ```

👉 Ce VLAN servira exclusivement pour l’administration.

## 🔐 Étape 5 : Activer et sécuriser l’accès SSH

1. Générer les clés RSA pour SSH :
   ```[HP] public-key local create rsa
   ```

2. Activer le service SSH (stelnet) :
   ```[HPSwitch] ssh server enable
   ```

3. Créer un utilisateur administrateur :

   ```[HP] local-user admin
   ```
   ```[HP-luser-admin] password simple MonMotDePasseFort
   ```
   ```[HP-luser-admin] service-type ssh
   ```
   ```[HP-luser-admin] authorization-attribute level 3
   ```
   
4. Configurer les sessions VTY pour n’autoriser que SSH :

   ```[HP] user-interface vty 0 4
   ```
   ```[HP-ui-vty0-4] authentication-mode scheme
   ```
   ```[HP-ui-vty0-4] protocol inbound ssh
   ```
   ```[HP-ui-vty0-4] quit
   ```

👉 Ainsi, Telnet est désactivé et seul SSH est autorisé.

## ✅ Étape 6 : Vérifications et tests

1. Vérifier l’état du stack IRF :

```
<HP> display irf
```

2. Vérifier l’adresse IP du VLAN de management :

```
<HP> display ip interface brief
```

3. Depuis un poste client, tester l’accès SSH :

```ssh admin@192.168.120.10
```

👉 Si tout est correct, la connexion doit s’établir en SSH avec l’utilisateur admin.


