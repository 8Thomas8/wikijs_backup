---
title: Mettre en FR le format du datepicker d' Angular Material
description: 
published: true
date: 2020-06-05T21:35:01.722Z
tags: 
---

# METTRE EN FR LE FORMAT DU DATEPICKER D'ANGULAR MATERIAL

Après avoir configurer le datepicker d'Angular Material, il est au format US.

Pour le passer en français, il faut ajouter ceci dans le `app.module.ts` .

```typescript
export const MY_FORMAT: MatDateFormats = {
	parse: {
		dateInput: 'DD/MM/YYYY',
	},
	display: {
		dateInput: 'DD/MM/YYYY',
		monthYearLabel: 'MMM YYYY',
		dateA11yLabel: 'DD/MM/YYYY',
		monthYearA11yLabel: 'MMMM YYYY',
	},
};
```

Puis dans les providers, il faut ajouter:
```typescript
providers: [
	{ provide: MAT_DATE_LOCALE, useValue: 'fr-FR' },
  { provide: MAT_DATE_FORMATS, useValue: MY_FORMAT }
]
```

A partir de là, l'affichage de la date choisie sera au format JJ/MM/AAAA.