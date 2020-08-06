---
title: Arrosage automatique
description: 
published: true
date: 2020-03-11T23:38:05.262Z
tags: 
editor: undefined
---

# ARROSAGE AUTOMATIQUE

Page de travail du propjet d'arrosage automatique sur un Raspberry Pi réalisé en python.

## Objectif

L'objectif est de:

1. Mesurer l'humidité de la terre périodiquement
1. Actionner une pompe sur une durée définit
1. Etre accessible depuis une page web

## Améliorations secondaires

Les axes d'amélioration suivant seront:

1. Détecter lorsque la réserve d'eau est vide
1. Notifier l'utilisateur quand la réserve d'eau est vide
1. Arrêter le système tant que la réserve d'eau n'a pas été remplie

## Ressources

* [Vidéo](https://www.youtube.com/watch?v=mQNJpWkdmbc)
* [Article](http://www.cyber-omelette.com/2017/09/automated-plant-watering.html)

## Matériel nécéssaire

* Raspberry Pi
* Alimentation 5V (Type portable)
* [Capteur humidité](https://www.amazon.com/gp/product/B00ZR3B60I/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=cyberomelette-20&creative=9325&linkCode=as2&creativeASIN=B00ZR3B60I&linkId=e9744f6328070e2f6725f008c6c25027)
* [Relais](https://www.amazon.com/gp/product/B0057OC5O8/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=cyberomelette-20&creative=9325&linkCode=as2&creativeASIN=B0057OC5O8&linkId=b0f6b4d50bd1543310f0eabd0846aa40)
* [Pompe](https://www.amazon.com/gp/product/B07F8JV7CT/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=cyberomelette-20&creative=9325&linkCode=as2&creativeASIN=B07F8JV7CT&linkId=0ad638a1eaaf8a0c0d354625d8a3a36b)
* [Tube](https://www.amazon.com/gp/product/B0002AQI9A/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=cyberomelette-20&creative=9325&linkCode=as2&creativeASIN=B0002AQI9A&linkId=07bdde096fb34fb31c785cb6dfd94e2b)

## Notes

* Programme fonctionnel (testé)
* Ecrire dans une base de donner postgresql plutôt que dans le fichier texte
* API à faire (login, récupèration dernier arrosage, activation mode auto, activation arrosage manuel)
* Suppression automatique des données de plus de 1 mois.
* Interface web à faire (Angular 9 ?)