---
title: "Servidor DNS con Bind"
date: 2021-09-11T22:21:06+02:00
draft: false
author: "Daniel Oscar Zamo"
---

> Este documento incluye la configuracion de:
>
> - Servidor DNS Bind sobre CentOS Stream 8 y derivados
> - El despliegue incluye dos vistas (una es LAN interna y la otra una vista WAN)
> - Se configuran las resoluciones directas e inversas
>

La representacion del despliegue se muestra en [esta captura][server.bind]

![Arq. inicial - serv. DNS bind, c/2 vistas][server.bind]

[server.bind]: /server.dns.bind.png "Arq. inicial - serv. DNS bind, c/2 vistas"

# Instalación y configuración red interna (vista interna)

## Instalar Bind

```
dnf -y install bind bind-utils
```

## Configurar red interna (vista interna)


En este ejemplo la red local esta definida en el rango `10.0.0.0/24`. El nombre de dominio es `my-asus.net`.  

### Fichero /etc/named.conf

```
vi /etc/named.conf

...
...
acl internal-network {
        10.0.0.0/24;
};
...
...
options {
... 
# Modificar las siguientes lineas
#       listen-on port 53 { 127.0.0.1; };
        listen-on port 53 { any; };
#       listen-on-v6 port 53 { ::1; };
        listen-on-v6 port 53 { any; };
...
...
# Se agrega la siguiente linea
       allow-transfer  { localhost; };
...
...

# Al final agrego las zonas de red y dominio

zone "my-asus.net" IN {
        type master;
        file "centos-stream-main.my-asus.net";
        allow-update { none; };
};
zone "0.0.10.in-addr.arpa" IN {
        type master;
        file "0.0.10.db";
        allow-update { none; };
};
```

### Conf. resolucion sobre IPV4

```
# Habilitar resolucion de DNS solo para IPV4
echo 'OPTIONS="-4"'  >> /etc/sysconfig/named
```

### Contenido ficheros
Se lista a continuacion el contenido de los ficheros modificados.

```
cat /etc/named.conf

//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//
acl internal-network {
        10.0.0.0/24;
};
options {
        listen-on port 53 { any; };
        listen-on-v6 port 53 { any; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { localhost; internal-network; };
        allow-transfer  { localhost; };
        recursion yes;
        dnssec-enable yes;
        dnssec-validation yes;
        managed-keys-directory "/var/named/dynamic";
        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
        /* https://fedoraproject.org/wiki/Changes/CryptoPolicy */
        include "/etc/crypto-policies/back-ends/bind.config";
};
logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};
zone "." IN {
        type hint;
        file "named.ca";
};
include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
zone "my-asus.net" IN {                                                                                                                                                                                                                     
        type master;
        file "centos-stream-main.my-asus.net";
        allow-update { none; };
};
zone "0.0.10.in-addr.arpa" IN {
        type master;
        file "0.0.10.db";
        allow-update { none; };
};

cat /etc/sysconfig/named
OPTIONS="-4"
```

## Configurar archivos de zonas


### Zona directa

```
cat /var/named/centos-stream-main.my-asus.net.lan

$TTL 86400
@   IN  SOA    centos-stream-main.my-asus.net.    root.my-asus.net. (
        2021110901  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
        IN  NS      centos-stream-main.my-asus.net.
        IN  A       10.0.0.30

centos-stream-main     IN  A    10.0.0.30
www                    IN  A    10.0.0.31
```

### Resolucion inversa

```
cat /var/named/0.0.10.db

$TTL 86400
@   IN  SOA     centos-stream-main.my-asus.net. root.my-asus.net. (
        2021110901  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
        IN  NS      centos-stream-main.my-asus.net.

30      IN  PTR     centos-stream-main.my-asus.net.
31      IN  PTR     www.my-asus.net.
```

### Verificar resolucion

#### Habilitar servicio DNS

```
# Habilitar e iniciar Bind
systemctl enable --now named

```

#### Habilitar en firewall

```
# Habilitar el servicio DNS si firewalld esta activado
firewall-cmd --add-service=dns --permanent
firewall-cmd --reload
```

#### Modificar el resolver, apuntando al aqui configurado (Si es necesario)

En esta caso la especion es:

- Ip DNS: `192.168.122.30/24`
- Interface: `enp1s0`

```
nmcli connection modify enp1s0 ipv4.dns 192.168.122.30
nmcli connection down enp1s0; nmcli connection up enp1s0
```

#### Probando resolucion

```
dig centos-stream-main.my-asus.net
dig -x 10.0.0.30
```
