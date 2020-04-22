---
title: Formater JSON (Javascript)
description: 
published: true
date: 2020-02-26T09:04:43.792Z
tags: 
---

# FORMATER JSON (JAVASCRIPT)

Lorsque l'on génère un JSON à partir d'un objet, en Javascript, il est possible de directement le formater, dans la méthode JSON.stringify(), pour qu'il soit plus lisible.
Il suffit de renseigner le paramètre spacer de la méthode (le 3ème) avec un Integer.


Exemple:

```javascript
var str = JSON.stringify(obj, null, 2);

// obj = Object: L'objet qui sera convertie en JSON
// null = Replacer : Une fonction qui remplace le résultat
// 2 = Spacer : Chiffre qui définit le nombre d'espace lors de la création de l'indentation automatique.
```