---
title: Utiliser NPM sans sudo
description: 
published: true
date: 2020-02-16T22:27:12.748Z
tags: 
editor: undefined
---

# Utiliser NPM sans la commande sudo

Suivre ces étapes:

1. Créer le dossier caché npm:
`mkdir ~/.npm`
1. Changer la configuration prefix de npm:
`npm config set prefix ~/.npm`
1. Editer le fichier .bashrc:
`nano ~/.bashrc`
1. Ajouter une variable d'environnement exportée à la fin du fichier:
`export PATH="$PATH:$HOME/.npm/bin"`
1. Prendre en compte les modifications:
`source ~/.bashrc`