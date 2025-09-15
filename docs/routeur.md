# Documentation de configuration du routeur #

1. Première étape : Réinitialiser le routeur a sa configuration d'origine sans le mot de passe admin. 

```
#Démarer le routeur et presser CTR+BREAK pour être en mode "rommon"
confreg 0x2142
reset
#Le routeur reboot sans configuration
enable
configure terminal
config-register 0x2102 #Permet de remettre le switch sur le bon registre
end
write
```
2. Changer le Nom du routeur 
```
enable 
config
hostname CHA-R1
```
3. Configurer la première interface Gigabit0/0
```
interface GigabitEthernet0/0
 ip address 183.44.28.1 255.255.255.252
 ip nat outside
 no shutdown
```
4. Configurer la deuxième interface Gigabit0/1
```
interface GigabitEthernet0/1
 no shutdown
```
<div class="annotate" markdown>

> Lorem ipsum dolor sit amet, (1) consectetur adipiscing elit.

</div>

1.  :man_raising_hand: I'm an annotation!