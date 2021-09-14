+++
title = "DNS y comando dig"
description = "DNS y comando dig"
date = "2021-09-14"
author = "Daniel Oscar Zamo"
+++

# DNS Consulta recursiva

![Representación consulta recursiva][query.recursive]

[query.recursive]: /images/dns.query.recursive.png "Representación consulta recursiva"

> dig (a secas), realiza una consulta de los servidores NS raíz

```
dig
```

> ¿Consulta de `www.axians.com` a un servidor de _**TLD**_ (dominios de nivel superior) `.com`?

```
dig ....
```

> ¿Consulta de `www.axians.com` a un servidor autoritativo de la zona?

```
dig
```

# Referencias

- https://root-servers.org/ <— servidores root
- https://eltallerdelbit.com/resolucion-dns-dig-linux/
- https://blog.standalonecomplex.es/2010/05/16/guia-de-dig/
- http://somebooks.es/comando-dig-ubuntu/
- https://rm-rf.es/como-usar-el-comando-dig-ejemplos/
- https://www.hostinger.es/tutoriales/comando-dig-linux
- https://www.administracionderedes.com/consulta-iterativa-recursiva/
- https://www.ibm.com/docs/en/zos/2.2.0?topic=uzudc-dig-command-query-name-servers
- https://learn.akamai.com/en-us/webhelp/pulsar-diagnostic-tools/pulsar-diagnostic-tools/GUID-80B4B9A1-16B2-4D70-8100-7B2617102002.html
- https://www.youtube.com/watch?v=sUhEqT_HSBI&ab_channel=ProfeSang <— Video explicado simple y bien
- https://www.cloudns.net/blog/10-most-used-dig-commands/ <— Ejemplo de como consultar un TLD (top level domain)
- https://www.cloudns.net/wiki/article/40/
- https://es.wikipedia.org/wiki/Sistema_de_nombres_de_dominio <— tipos de registros DNS
- https://stackoverflow.com/questions/706077/dns-how-to-dig-thru-the-right-domain-name-from-the-tld
- https://www.youtube.com/watch?v=LqSqrtxrW7w&ab_channel=DefectoDigital <— buena explicacion de DNS
- https://www.youtube.com/watch?v=PLTndeCSdzk&ab_channel=UniversitatPolit%C3%A8cnicadeVal%C3%A8ncia-UPV
