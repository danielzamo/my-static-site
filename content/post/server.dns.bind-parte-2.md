---
title: "Servidor DNS con BIND - parte II"
date: 2021-09-12T02:14:42+02:00
draft: false 
---

> Este documento es una segunda parte de la conf. del servidor de DNS bind. En particular se expone la configuracion de las resoluciones de los elementos de la red externan.
>

# BIND: configurar red externa

En esta seccion, se configura BIND sobre la red externa (aunque tiene asignado un Ip privado en realidad, es a fines practicos contextual). La especificacion de la red externa configurada es 192.168.122.0/24. El nombre de dominio es my-asus.net. El ip del servidor es 192.168.122.30.

