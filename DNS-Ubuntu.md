# Création DNS Debian

## Sur le serveur 

- On installe bind9 :
```Bash
$ sudo apt update
$ sudo apt install -y bind9 bind9utils bind9-doc dnsutils
```

- On configue le fichier **named.conf.options**
```Bash
  
$ sudo nano /etc/bind/named.conf.options



acl internal-network {
192.168.0.0/24;
};
options {
        directory "/var/cache/bind";
        allow-query { localhost; internal-network; };
        allow-transfer { localhost; };
        forwarders { 8.8.8.8; };
        recursion yes;
        dnssec-validation auto;
        listen-on-v6 { any; };
};

```



- On configue le fichier **named.conf.local**
```Bash
  
$ sudo nano named.conf.local


zone "wilders.lan" IN {
        type master;
        file "/etc/bind/forward.wilders.lan";
        allow-update { none; };
};
zone "0.168.192.in-addr.arpa" IN {
        type master;
        file "/etc/bind/reverse.wilders.lan";
        allow-update { none; };
};
```


- On va maintenant configurer les zones **forward** et **reverse**.
- On utilise les sample zone files (db.local pour forward et db.127 pour reverse)
- Pour le **forward lookup zone file** : 
```Bash
$ cd /etc/bind
$ sudo cp db.local forward.wilders.lan
$ sudo nano forward.wilders.lan
$TTL 604800
@ IN SOA primary.linuxtechi.local. root.primary.wilders.lan. (
         2022072651 ; Serial
         3600 ; Refresh
         1800 ; Retry
         604800 ; Expire
         604600 ) ; Negative Cache TTL
;Name Server Information
@ IN NS primary.wilders.lan.

;IP address of Your Domain Name Server(DNS)
primary IN A 192.168.0.40

;Mail Server MX (Mail exchanger) Record
wilders.lan. IN MX 10 mail.linuxtechi.local.

;A Record for Host names
www IN A 192.168.0.50
mail IN A 192.168.0.60

;CNAME Record
ftp IN CNAME www.wilders.lan.
```

- Pour le **reverse lookup zone file** :
```Bash
$ sudo cp db.127 reverse.wilders.lan
$ sudo nano /etc/bind/reverse.wilders.lan
$TTL 86400
@ IN SOA linuxtechi.local. root.wilders.lan. (
         2022072752 ;Serial
         3600 ;Refresh
         1800 ;Retry
         604800 ;Expire
         86400 ;Minimum TTL
)
;Your Name Server Info  
@ IN NS primary.wilders.lan.  
primary IN A 192.168.0.40  
;Reverse Lookup for Your DNS Server  
40 IN PTR primary.wilders.lan.  
;PTR Record IP address to HostName  
50 IN PTR www.wilders.lan.  
60 IN PTR mail.wilders.lan.
```

- On modifie ensuite les options dans le fichier /etc/default/named :
```Bash
OPTIONS="-u bind -4"
```

Puis on lance et on active le **named** pour que cette configuration soit lancée au démarrage du serveur : 
```Bash
$ sudo systemctl start named
$ sudo systemctl enable named
```

- Pour voir le statut du service bind :
```Bash
$ sudo systemctl status named
```

- Si on doit désactiver le parefeu :
```Bash
$ sudo ufw allow 53  
Rule added  
Rule added (v6)
```

- Pour vérifier les syntaxes :
- Pour **named.conf.local** :
```Bash
$ sudo named-checkconf /etc/bind/named.conf.local
```

- Pour les **forward et reverse lookup zone** :
```Bash
$ sudo named-checkzone wilders.lan /etc/bind/forward.wilders.lan
# Affiche :zone wilders.lan/IN: loaded serial 2022072651
# Affiche : OK

$ sudo named-checkzone wilders.lan /etc/bind/reverse.wilders.lan
# Affiche : zone wilders.lan/IN: loaded serial 2022072752
# Affiche : OK

```


## Sur le client : 


- On inscrit le serveur DNS manuellement dans la partie IPv4 --> DNS manuel
- On configure le fichier **/etc/resolv.conf** :
```Bash
$ sudo nano /etc/resolv.conf
search wilders.lan
nameserver 192.168.0.40
```

- On teste avec **dig** :
```Bash
$ dig primary.wilders.lan
```

- Pour tester le reverse :
```Bash
$ dig -x 192.168.0.40
```

- Pour tester la sortie de la commande **dig** :
```Bash
nslookup primary.wilders.lan
```




