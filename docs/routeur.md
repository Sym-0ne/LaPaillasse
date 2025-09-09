Première étape : Réinitialiser le routeur a sa configuration d'origine sans le mot de passe admin. 

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