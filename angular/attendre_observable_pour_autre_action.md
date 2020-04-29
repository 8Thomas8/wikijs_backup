---
title: Attendre une action sur un observable pour faire une autre action
description: 
published: true
date: 2020-04-29T10:48:41.011Z
tags: typescript, développement, angular, observable, rxjs, promise
---

# ATTENDRE UNE ACTION SUR UN OBSERVABLE POUR FAIRE UNE AUTRE ACTION (Promise)

Pour attendre qu'une action soit faite, il faut une promise, une fois cette promise réalisé, on effectue la seconde action.

Par exemple, si on veut faire la méthode 1 sur l'observable, puis la méthode 2 qui fera autre chose:
```typescript
const promise = this.exempleObserbale.methode1().toPromise();
promise.then(() => this.methode2());
```