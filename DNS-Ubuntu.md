# Création DNS Debian

# Sur le serveur 

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


zone "linuxtechi.local" IN {
        type master;
        file "/etc/bind/forward.linuxtechi.local";
        allow-update { none; };
};
zone "0.168.192.in-addr.arpa" IN {
        type master;
        file "/etc/bind/reverse.linuxtechi.local";
        allow-update { none; };
};
```


- On va maintenant configurer les zones **forward** et **reverse**.
- 
