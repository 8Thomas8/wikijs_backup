---
title: FICHIER CSV - FORMATGE UTF8 / BOM
description: 
published: true
date: 2020-03-30T07:42:36.670Z
tags: 
editor: undefined
---

# FICHIER CSV - FORMATAGE UTF8 / BOM

Les fichiers CSV de base sont formatés sans utiliser de BOM (Byte Order Mark).

Ainsi, lorsque l'on créé un nouveau CSV, qui contient des accents, qu'on le sauvegarde, et qu'on le réouvre, les accents ne sont pas gérés (mais encore visible grâce à notepad+ par exemple). Et si à ce moment on le sauvegarde de nouveau, on pert les accens, même dans notepad++.

Pour forcer les CSV à s'ouvrir en gérant les accents, ils ont besoin d'un BOM qui est placé dans les 3 premiers byte du fichier.
Le BOM est un caractère invisible que l'on peut récupérer de cette façon :

```java
// Exemple en JAVA
String UTF8_BOM = "\uFEFF"
```

Une fois ce caractère placé en debut de fichier, le CSV s'ouvrira directement avec la gestion des accents.

Pour créer un CSV avec ce BOM, sans le générer via un code, il faut créer un .csv avec notepad++, et l'encoder en UT8 avec BOM (via la barre d'outils).

> **ATTENTION:**
Lorsque l'on souhaite comparer le contenu de la première ligne d'un fichier, avec des String, dans un code, il faut penser à supprimer ce caractère invisible, avant de traiter le contenu du fichier, sinon on aura beau avoir la même string, celle issue du fichier contiendra un caractère invisible supplémentaire qui faussera toute comparaison.
{.is-warning}

> **TRAITEMENT:**
Pour gérer le BOM coté code, il suffit de supprimer ce caractère si il est présent en tout début de document, dès la récupération du document, puis il suffit de le rajouter à la même place, avant de retourner le document.
{.is-success}

