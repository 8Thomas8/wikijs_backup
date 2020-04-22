---
title: La condition dans un for
description: 
published: true
date: 2020-02-21T10:48:57.514Z
tags: 
---

# LA CONDITION DANS UN FOR

Dans un for `for(int i = 0, i < 6; i++)`, la partie du milieu, avec la comparaison, peut être plus complexe et disposer de plusieurs conditions.

Exemple:

```java
for (int i = 0; i < 3 && cache.get(j) != null; j++) {
		...
}
```

Cela permet de supprimer des conditions inutiles, et de simplifier le code.