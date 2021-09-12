---
title: "Servidor DNS con Bind"
date: 2021-09-11T22:21:06+02:00
draft: false
author: "Daniel Oscar Zamo"
---

> Este documento incluye la configuracion de:
>
> - Servidor DNS Bind sobre CentOS Stream 8 y derivados
> - El despliegue incluye dos redes (una es LAN interna y la otra una WAN
> - Se configuran las resoluciones directas e inversas
> - Se definen dos vistas.
>

La representacion del despliegue se muestra en [esta captura][server.bind]

![Arq. inicial - serv. DNS bind, c/2 interface sobre redes diferentes][server.bind]

[server.bind]: /server.dns.bind.png "Arq. inicial - serv. DNS bind, interface sobre redes diferentes"

# Instalación y configuración red interna

## Instalar Bind

```
dnf -y install bind bind-utils
```

## Configurar red interna

En este ejemplo la red local esta definida en el rango `10.0.0.0/24`. El nombre de dominio es `my-asus.net`.  

### Fichero /etc/named.conf

```
cat /etc/named.conf

acl internal-network { 10.0.0.0/24; };
acl external-network { 192.168.122.0/24; };
options {
        listen-on port 53 { any; };
        listen-on-v6 { any; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { localhost; internal-network; external-network; };
        allow-transfer  { localhost; };
        recursion yes;
        dnssec-enable yes;
        dnssec-validation yes;
        managed-keys-directory "/var/named/dynamic";
        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
        include "/etc/crypto-policies/back-ends/bind.config";
};
logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};
view "internal" {
        match-clients { localhost; internal-network; };
        zone "." IN { type hint; file "named.ca"; };
        zone "my-asus.net" IN { type master; file "my-asus.net.lan"; allow-update { none; }; };
        zone "0.0.10.in-addr.arpa" IN { type master; file "0.0.10.db"; allow-update { none; }; };
        include "/etc/named.rfc1912.zones";
        include "/etc/named.root.key";
};
view "external" {
        match-clients { any; };
        allow-query { any; };
        recursion no;
        zone "my-asus.net" IN { type master; file "my-asus.net.wan"; allow-update { none; }; };
        zone "30.122.168.192.in-addr.arpa" IN { type master; file "30.122.168.192.db"; allow-update { none; }; };
};

```

### Conf. resolucion solo en IPV4

```
# Habilitar resolucion de DNS solo para IPV4
echo 'OPTIONS="-4"'  >> /etc/sysconfig/named
```

## Configurar archivos de zona interna

### Resolucion directa

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

## Configurar archivos de zona external

### Resolucion directa

```
cat /var/named/30.122.168.192.db

$TTL 86400
@   IN  SOA     centos-stream-main.my-asus.net. root.my-asus.net. (
        2021110903  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
        IN  NS      centos-stream-main.my-asus.net.

30      IN  PTR     centos-stream-main.my-asus.net.
31      IN  PTR     www.my-asus.net.

```

### Resolucion inversa

```
at /var/named/my-asus.net.wan

$TTL 86400
@   IN  SOA    centos-stream-main.my-asus.net.    root.my-asus.net. (
        2021110903  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
        IN  NS      centos-stream-main.my-asus.net.
        IN  A       192.168.122.30

centos-stream-main     IN  A    192.168.122.30
www                    IN  A    192.168.122.31

ns1                    IN  CNAME    centos-stream-main.my-asus.net.

```

## Verificar resolucion

### Habilitar servicio DNS

```
# Habilitar e iniciar Bind
systemctl enable --now named

```

### Habilitar en firewall

```
# Habilitar el servicio DNS si firewalld esta activado
firewall-cmd --add-service=dns --permanent
firewall-cmd --reload
```

### Modificar el resolver, apuntando al aqui configurado (Si es necesario)

En esta caso la especion es:

- Ip DNS: `192.168.122.30/24`
- Interface: `enp1s0`

```
nmcli connection modify enp1s0 ipv4.dns 192.168.122.30
nmcli connection down enp1s0; nmcli connection up enp1s0
```

### Probando resolucion

```
dig centos-stream-main.my-asus.net
dig -x 10.0.0.30
```
