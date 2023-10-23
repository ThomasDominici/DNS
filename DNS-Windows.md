# Création de DNS Windows

## Sur le serveur : 

- Dans le server manager, ajouter le rôle DNS
- suivre les instructions
- DNS, clic droit, DNS manager
- clic droit, créer forward lookup zones
- Mettre le nom **wilders.lan**
- clic droit, créer reverse lookup zones -> en IPv4 -> renseigner la plage IP du serveur 


- Dans la zone Forward, clic droit, new host (on renseigne le nom et l'adresse IP), ici **test**
- De même, on fait l'alias CName : clic droit, new alias (on renseigne son nom et on browse pour trouver l'hôte de l'alias

- Dans l'interface graphique ethernet, on renseigne l'IP de notre serveur DNS manuellement
- ON teste notre DNS :
```Bash
nslookup test.wilders.lan
```


## Sur le client : 

- De même que sur le serveur, on va voir l'adapter ethernet et on inscrit manuellement l'adresse IP du serveur DNS
- On test : 
```Bash
nslookup test.wilders.lan
```
