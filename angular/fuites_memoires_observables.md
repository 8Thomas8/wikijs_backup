---
title: Fuites Mémoires - Observables
description: 
published: true
date: 2020-03-06T09:31:20.441Z
tags: 
editor: undefined
---

# FUITES MEMOIRES - OBSERVABLES

Pensez à se désinscrire sous peine d'avoir une fuite mémoire. Plusieurs observables peuvent se créer si il est sur un composant qui se ferme et revient plusieurs fois, et les observables restent utilisables.

Il est possible d'utiliser le pipe `| async` dans le html ou alors d'utiliser l'opérateur RxJS takeUntil.

```typescript=
// Imports nécessaires
import { takeUntil } from 'rxjs/operators';
import { Subject } from 'rxjs/Subject';

divContacts = false;

// Observable nécessaire pour takeUntil
private readonly _destroyed$ = new Subject();


ngOninit() {
    this.toggleDivContactService.getStatus().pipe(takeUntil(this._destroyed$)).subscribe(status => (this.divContacts = status));
}
```