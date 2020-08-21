---
title: Supprimer toute trace d'un fichier
description: 
published: true
date: 2020-04-22T13:12:28.354Z
tags: 
<<<<<<< HEAD
editor: undefined
=======
>>>>>>> 0acacff286497f9e891768325d8b837e0020dc2c
---

# SUPPRIMER TOUTE TRACE D'UN FICHIER

> Cette action est irréversible et peut causer des instabilités sur des versions antérieurs de l'application.
{.is-danger}

> Cette action va modifier les identifiants des commits modifiés par la suppression. Problèmatique pour le travail en collaboration.
{.is-warning}

## 1. Commande
`git filter-branch -f --tree-filter "rm -rf <chemin vers le fichier>" --prune-empty -- --all`

## 2. Pousser les modifications
`git push origin --force --all`
Le paramètre force est nécessaire pour réécrire tout l'historique et all pour le faire sur toutes les branches.

> Si toutes les branches ne sont pas à pousser, mais seulement les distances: `git push origin --force`.
{.is-info}

> Tout le monde doit rebase le projet (dans le cas contraire, un merge pourrait faire réapparaitre le fichier).
{.is-danger}


## 3. Explication de la commande

### `git filter-branch`
Va permettre de ré-écrire complètement l’historique en fonction des paramètres que nous allons passer ensuite.

### `-f`
Forcer le démarrage de git filter-branch si jamais il y a déjà un dossier temporaire refs/original/ pré-existant. Git s’en sert pour faire une sauvegarde de l’historique avant de partir à l’aventure.

> Le -f n’est donc pas obligatoire mais si le backup ne peut pas être créé, git vous dira de relancer la commande avec cette option.
{.is-info}


### `--tree-filter "<shell command>"`
Cette option va vous permettre de passer en revue chacun de vos commits et d’y exécuter la commande que vous aurez passé en paramètre.

### `rm -rf <chemin vers le fichier>` 
Supprimer, et forcer la suppression du fichier. Il faut forcer la commande (avec -rf) car le nettoyage s’arrête dès qu'il ne trouve pas le fichier demandé dans un commit.

> Pour juste retirer le fichier de l’historique git, utiliser `—index-filter “git rm -rf —cached —ignore-unmatch <chemin vers le fichier>”` à la place.
{.is-info}


### `--prune-empty`
Suppresion des commits vides après réécriture.

### `-- --all`
Le premier -- permet de distinguer --all comme une options supplémentaire, et non pas un paramètre de --prune-empty. Cela permet de réaliser l'opération sur toutes les branches.

