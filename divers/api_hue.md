---
title: API Phillips Hue
description: 
published: true
date: 2020-03-30T21:23:31.297Z
tags: 
<<<<<<< HEAD
editor: undefined
=======
>>>>>>> 0acacff286497f9e891768325d8b837e0020dc2c
---

# API PHILLIPS HUE 

## Eteindre le groupe Alarme

Viser l'url:
`/api/<token>/groups/10/action`

Passer le body:
```json
{
  "on": false
}
```

La réponse attendue est:
```json
[
	{
		"success": {
			"/groups/10/action/on": false
		}
	}
]
```