---
title: Récupérer le caractère de saut de ligne
description: 
published: true
date: 2020-02-21T11:05:10.703Z
tags: 
---

# RECUPERER LE CARACTERE DE SAUT DE LIGNE

## Quelque soit le système d'exploitation

```java
String newLine = System.getProperty("line.separator");
```

Ensuite, il n'y a plus qu'à utiliser cette variable là où l'on veut sauter une ligne, sans un log ou un sysout.