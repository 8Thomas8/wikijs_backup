---
title: La propriété object-fit
description: 
published: true
date: 2020-08-06T17:16:53.699Z
tags: 
editor: undefined
---

# La balise object-fit en CSS

## Exemple de code CSS

On va utiliser un container qui va déterminer la taille que l'image pourra occuper. Et un enfant, qui est l'image, qui va s'adapter à 100% de la hauteur et de la largeur du container, en fonction de l'option de la balise `object-fit`.

```css
.container {
    width: 150px;
    height: 80px;
}

.child {
    width: 100%;
    height: 100%;
    object-fit: contain;
}
```

## Explications pour les valeurs:

`object-fit: contain` Le contenu remplacé est dimensionné pour maintenir ses proportions tout en étant ajusté à la boîte de contenu : sa taille réelle est résolue en utilisant la largeur et la hauteur de l'élément comme contraintes de contenant.

`object-fit: cover` Le contenu remplacé est dimensionné pour maintenir son ratio d'affichage tout en remplissant toute la boîte de contenu. La taille réelle est calculée pour couvrir la zone décrite par la hauteur et la largeur de l'élément. Si les ratios de l'objet et de la boîte ne correspondent pas, le contenu remplacé sera rogné.

`object-fit: fill`
Le contenu remplacé est dimensionné pour remplir la boîte de contenu. La taille réelle de l'objet est déterminée grâce à la hauteur et à la largeur de l'élément. Il est donc étiré afin de remplir la boîte.

`object-fit: none`
Le contenu remplacé n'est pas redimensionné à l'intérieur de l'élément.

`object-fit:scale-down`
Le contenu est dimensionné comme si none ou contain étaient spécifiés, on prend celui qui aboutirait à une taille réelle plus petite.