---
title: Restauration de W10 depuis la console de récupération
description: 
published: true
date: 2020-05-06T01:08:00.132Z
tags: windows, restauration, console
---

# RESTAURATION W10 VIA CONSOLE DE RECUPERATION

## Restauration Système
> Après plusieurs Blue Screen, Windows entre en mode de Récupération automatique.
> {.is-info}

1. Choisir Options avancées
1. Choisir Restauration du système.

## Restauration en ligne de commande
> Si ça ne fonctionne pas (Erreur en lien avec la protection système...):
> {.is-warning}

1. Choisir Options avancées
1. Choisir Invite de commandes
1. Entrer rstrui /offline:C

## Restauration en ligne de commande, avec renommage SYSTEM et SOFTWARE
> Si ça ne fonctionne pas, avec une erreur qui parle de registre:
> {.is-danger}

1. Choisir Options avancées
1. Choisir Invite de commandes
1. Entrer C:Windows\system32\config
1. Entrer ren SYSTEM system.001
1. Entrer ren SOFTWARE software.001
1. Entrer rstrui /offline:C

## C'est terminé
> Une fois la récupération effectuée, pensez à supprimer le programme/pilote qui posait problème.
> {.is-success}
