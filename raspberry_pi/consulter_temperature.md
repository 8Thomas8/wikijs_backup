---
title: Consulter la température du Raspberry Pi
description: 
published: true
date: 2020-02-15T23:35:31.514Z
tags: 
<<<<<<< HEAD
editor: undefined
=======
>>>>>>> 0acacff286497f9e891768325d8b837e0020dc2c
---

# CONSULTER LA TEMPERATURE DU RASPBERRY PI

## Consultation simple

Pour consulter la température d'un Raspberry Pi, il suffit de lancer les commandes suivantes dans le terminal:

Pour obtenir une valeur de type **temp=44.9°C**

`/opt/vc/bin/vcgencmd measure_temp`

Pour obtenir une valeur de type **44912**

`cat /sys/class/thermal/thermal_zone0/temp`

## Divers

_Il serait intéressant de concevoir un programme qui relève la température régulièrement, et la transmet sur une page web ou autre._