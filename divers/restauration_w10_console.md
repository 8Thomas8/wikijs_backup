---
title: Restauration de W10 depuis la console de récupération
description: 
published: true
date: 2020-05-06T01:04:48.767Z
tags: windows, restauration, console
---

# RESTAURATION W10 VIA CONSOLE DE RECUPERATION

> Après plusieurs Blue Screen, Windows entre en mode de Récupération automatique.
> 
{.is-info}

1. Choisir Options avancées
1. Choisir Restauration du système.

> Si ça ne fonctionne pas (Erreur, protection système...):
> 
{.is-warning}

1. Choisir Options avancées
1. Choisir Invite de commandes
1. Entrer rstrui /offline:C

> Si ça ne fonctionne pas, avec une erreur qui parle de registre:
> 
{.is-danger}

1. Choisir Options avancées
1. Choisir Invite de commandes
1. Entrer C:Windows\system32\config
1. Entrer ren SYSTEM system.001
1. Entrer ren SOFTWARE software.001
1. Entrer rstrui /offline:C

> Une fois la récupération effectuée, pensez à supprimer le programme/pilote qui posait problème.
> 
{.is-success}
