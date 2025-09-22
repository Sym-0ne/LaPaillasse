# Tutoriel complet : Installer Proxmox VE avec RAID 5 + LVM (4×2 To)

Ce guide explique comment installer **Proxmox VE** sur un serveur avec **4 SSD de 2 To** en configurant :  
- **200 Go** → OS Proxmox  
- **200 Go** → stockage ISO/OVA  
- **~5.6 To** → stockage VM  
Le tout sur **RAID 5 logiciel (mdadm) + LVM**.

---

## 1. Préparer le serveur
1. Télécharger l’ISO de Proxmox VE :  
   👉 [https://www.proxmox.com/en/downloads](https://www.proxmox.com/en/downloads)  
2. Créer une clé USB bootable.  
3. Booter le serveur sur la clé USB.

---

## 2. Installer Proxmox OS (200 Go)
1. Au démarrage, choisir **Install Proxmox VE**.  
2. Quand il demande le disque :  
   - Sélectionner **un SSD de 2 To**.  
   - Dans les **Options avancées**, choisir **LVM**.  
   - Limiter la partition racine (`/`) à **200 Go**.  
   - Laisser le reste du disque libre (il servira pour le RAID).  
3. Terminer l’installation (langue, clavier, IP réseau, mot de passe root).  
4. Redémarrer → Proxmox est installé et fonctionne sur **200 Go**.

---

## 3. Préparer les disques pour RAID 5
### Identifier les disques
Lister les disques disponibles :
```bash
lsblk -o NAME,SIZE,MODEL

Exemple de sortie 

sda   1.8T Samsung_SSD
sdb   1.8T Samsung_SSD
sdc   1.8T Samsung_SSD
sdd   1.8T Samsung_SSD

sda1 = 200 Go pour Proxmox OS.
sda2, sdb, sdc, sdd serviront au RAID 5.

4. Créer le RAID 5 logiciel

Créer le RAID 5 sur les 4 disques :

mdadm --create --verbose /dev/md0 --level=5 --raid-devices=4 /dev/sda2 /dev/sdb /dev/sdc /dev/sdd
Vérifier la synchronisation :
cat /proc/mdstat
Enregistrer la configuration RAID pour qu’elle soit reconnue au boot :
mdadm --detail --scan >> /etc/mdadm/mdadm.conf
update-initramfs -u

5. Configurer LVM au-dessus du RAID

Initialiser le RAID comme Physical Volume
pvcreate /dev/md0

Créer le Volume Group
vgcreate proxmox-vg /dev/md0

Créer les Logical Volumes

200 Go pour ISO/OVA
lvcreate -L 200G -n iso-ova proxmox-vg
mkfs.ext4 /dev/proxmox-vg/iso-ova

Tout le reste (~5.6 To) pour VM
lvcreate -l 100%FREE -n vm-storage proxmox-vg
mkfs.ext4 /dev/proxmox-vg/vm-storage

6. Monter les volumes

Créer les répertoires de montage :
mkdir -p /mnt/iso-ova /mnt/vm-storage


Éditer /etc/fstab pour monter automatiquement les volumes :
/dev/proxmox-vg/iso-ova    /mnt/iso-ova    ext4 defaults 0 2
/dev/proxmox-vg/vm-storage /mnt/vm-storage ext4 defaults 0 2

Appliquer et vérifier :
mount -a
df -h

7. Ajouter les stockages dans Proxmox
ISO/OVA
Aller dans Datacenter → Storage → Add → Directory
Remplir :
ID : iso-ova
Directory : /mnt/iso-ova
Content : ISO image, Container template
VM
Aller dans Datacenter → Storage → Add → Directory
Remplir :
ID : raid5-vm
Directory : /mnt/vm-storage
Content : Disk image, VZDump backup file

8. Vérification finale

Vérifier le RAID :
mdadm --detail /dev/md0

Vérifier LVM :
lvs
vgs
pvs

Vérifier les montages :
df -h

Dans Proxmox, les stockages doivent apparaître :

local (200 Go, OS Proxmox)
iso-ova (200 Go)
raid5-vm (~5.6 To)