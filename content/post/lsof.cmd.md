---
title: "El comando lsof"
date: 2021-09-15T22:34:53+02:00
draft: false
---

How to list all open files
To list all open files, run the lsof command without any arguments:
lsof

# Listar todos los archivos abiertos

Para listar todos los archivos abiertos, ejecutar `lsof` sin argumentos

```
lsof
```
# Listar archivos abiertos por procesos pertenecientes a un usuario en particular

```
lsof -u <USER-NAME>
```

# Referencias

- [Linux lsof command][lsof.command]

[lsof.command]: https://www.howtoforge.com/linux-lsof-command/ "Comando Linux lsof"
