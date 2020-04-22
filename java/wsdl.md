---
title: WSDL
description: 
published: true
date: 2020-02-18T22:32:18.558Z
tags: 
---

# Le WSDL

Lié au webservice, et au standard SOAP.

Le fichier WSDL décrit un webservice. Il spécifit la localisation et les méthodes du service. Il utilise les éléments majeurs suivant:

* `<types>`	Définit (avec un schéma XML) le type de données utilisés par le webservice.
* `<message>`	Définit les élements des données, pour chaque opération.
* `<portType>` Décrit les opérations qui peuvent être faites, et les méssages impliqués.
* `<binding>` Définit le protocol, et le format des données pour chaque type de port.