---
title: "Dns"
date: 2021-09-13T21:13:21+02:00
draft: false
---

> Consulta recursiva

![Rep. consulta recursiva][dns.recursive]

[dns.recursive]:/images/dns.query.recursive.png "Representación consulta recursiva"

> El comando `dig` , realiza una consulta de los servidores raíz (server root)

```
dig
```

> ¿Cuáles son los servidores de nombres, responsables de los TLD (dominios de nivel superior) `.com`?
> _Ejemplo los de_ `com`

```
dig @a.root-servers.net. com
```

> Consultar `axians.com` sobre uno de los TLD `com`

```
dig @e.gtld-servers.net. axians.com
```

# Referencias

- Servidores [root server][root.server]

[root.server]: https://root-servers.org/ "servidores root server DNS"

- https://eltallerdelbit.com/resolucion-dns-dig-linux/
- https://blog.standalonecomplex.es/2010/05/16/guia-de-dig/
- http://somebooks.es/comando-dig-ubuntu/
- https://rm-rf.es/como-usar-el-comando-dig-ejemplos/
- https://www.hostinger.es/tutoriales/comando-dig-linux
- https://www.administracionderedes.com/consulta-iterativa-recursiva/
- https://www.ibm.com/docs/en/zos/2.2.0?topic=uzudc-dig-command-query-name-servers
- https://learn.akamai.com/en-us/webhelp/pulsar-diagnostic-tools/pulsar-diagnostic-tools/GUID-80B4B9A1-16B2-4D70-8100-7B2617102002.html
- https://www.youtube.com/watch?v=sUhEqT_HSBI&ab_channel=ProfeSang   <--- Video explicado simple y bien
- https://www.cloudns.net/blog/10-most-used-dig-commands/     <--- Ejemplo de como consultar un TLD (top level domain)
- https://www.cloudns.net/wiki/article/40/
- https://es.wikipedia.org/wiki/Sistema_de_nombres_de_dominio <--- tipos de registros DNS
- https://stackoverflow.com/questions/706077/dns-how-to-dig-thru-the-right-domain-name-from-the-tld
- https://www.youtube.com/watch?v=LqSqrtxrW7w&ab_channel=DefectoDigital   <--- buena explicacion de DNS
- https://academy.bit2me.com/en/what-is-dns-server/
