---
title: PWA: Le Service Worker
description: 
published: true
date: 2020-08-14T10:48:45.576Z
tags: javascript, pwa, service worker
editor: markdown
---

# PWA: LE SERVICE WORKER

Ce qui est important à gérer c'est la gestion du cache.
L'autre chose très importante est la stratégie d'accès aux requetes:
- Cache first
- Network first
- Race cache/network (le premier dispo sert le résultat)

```javascript
// La version permet de gérer la gestion du cache en fonction de la version de Service Worker.
const version = 'v0:0:1'

// Path des fichiers statics à mettre en cache.
let urlsToCache = [
    // Pages
    '/'
]

// Clés du manifest.json pour récupérer le path des mêmes fichiers versionnés qui seront à mettre en cache pour un proket webpack.
const filesToCache = [
		''
]

console.log("SW: Chargement...");

// Mise en cache des assets s'ils ne le sont pas déjà.
const cacheAsset = (url) => {
    return caches.open(version + 'fundamentals')
        .then(cache => {
            const request = new Request(url);
            return cache.match(request)
                .then(response => {
                    if (!response) {
                        return fetch(request)
                            .then(
                                response => {
                                    return cache.put(
                                        request,
                                        response.clone()
                                    )
                                }
                            )
                    }
                })
        })
}

// Installation du service worker.
self.addEventListener("install", event => {
    let filesUrl = [];

    console.log("SW : Installation en cours.");

    event.waitUntil(
        fetch('./build/front/manifest.json').then(resp => {
            resp.json()
                .then((json) => {
                    console.log('SW : Récupération des urls de fichier versionnés à cacher depuis le manifest.json.');
                    Object.keys(json).forEach(key => {
                        if (filesToCache.indexOf(key) !== -1) {
                            filesUrl.push(json[key]);
                        }
                    })
                    Promise.resolve(filesUrl).then(() => {
                        urlsToCache = urlsToCache.concat(filesUrl);
                    });
                }).then(() => {
                console.log('SW : Mise en cache des fichiers statics + fichiers versionnés.');
                Promise.all(
                    urlsToCache.map(url => cacheAsset(url)),
                    console.log("SW : Installé."),
                    self.skipWaiting()
                )
            });
        })
    )
});

// Activation du service worker.
self.addEventListener("activate", event => {
    console.log("SW: Activation en cours.");

    // Suppression du cache si il ne correspond pas à la version du SW.
    event.waitUntil(
        caches
            .keys()
            .then(keys => {
                return Promise.all(
                    keys
                        .filter(key => {
                            return !key.startsWith(version)
                        })
                        .map(key => {
                            return caches.delete(key)
                        })
                );
            })
            .then(() => {
                console.log('SW: Activé');
            })
    );
});

// Retourne une réponse pour les requêtes impossibles à joindre
function unableToResolve() {
    return new Response(
        '<h1 style="text-align: center;">Service indisponible</h1>', {
            status: 503,
            statusText: 'Service Unavailable',
            headers: new Headers({
                'Content-Type': 'text/html'
            })
        });
}

// Interception des requêtes et application de la stratégie network first.
self.addEventListener("fetch", event => {

    const req = event.request;

    // Ignore les requêtes qui ne sont pas des GET.
    if (req.method !== 'GET') {
        console.log('SW Fetch: Récupération de la requête abandonné ( != \'GET\' )', req.url);
        return;
    }

    // Ignore les requêtes qui sont hors site.
    if (req.url.indexOf('degrouptest') < -1) {
        console.log('SW Fetch: Récupération de la requête abandonné ( hors site )', req.url);
        return;
    }

    // Ignore les requêtes qui ne sont pas de mode navigate.
    if (req.mode !== 'navigate') {
        console.log('SW Fetch : Récupération de la requête abandonné ( mode !== navigate )', req.url);
        return;
    }

    event.respondWith(
        fetch(req)
            .then(
                response => {
                    // Clone la réponse pour l'utiliser une seconde fois.
                    let cacheCopy = response.clone();

                    // Enregistre la réponse clonée dans la cache.
                    caches
                        .open(version + 'pages')
                        .then(
                            cache => {
                                cache.put(req, cacheCopy);
                            }
                        )
                        .then(() => {
                            console.log('SW Fetch: Réponse récupéré online.', req.url);
                            console.log('SW Fetch: Réponse online ajouté au cache.', req.url);
                        });

                    // Sert la réponse obtenu online.
                    return response;
                },
                // Fallback si pas de réponse online.
                () => {
                    console.log('SW Fetch : Impossible de récupérer la réponse online.', req.url);
                    console.log('SW Fetch : Essai de récupération depuis le cache.', req.url);


                    // Sert la réponse obtenue dans le cache.
                    return caches.match(req)
                        .then(
                            // Réponse du cache.
                            response => {
                                if (response) {
                                    console.log('SW Fetch : Récupération de la réponse depuis le cache', req.url);
                                    return response;
                                } else {
                                    console.log('SW Fetch : Impossible de récupérer la réponse depuis le cache', req.url);
                                    return unableToResolve();
                                }
                            })
                        .catch(() => {
                            console.log('SW Fetch : Impossible de récupérer la réponse depuis online ou le cache', req.url);
                            return unableToResolve();
                        })
                }
            ))
});

```


Event fetch avec cache first et mise à jour du cache derrière pour prochain chargement.
```javascript
self.addEventListener("fetch", function(event) {
  console.log('WORKER: fetch event in progress.');

  /* We should only cache GET requests, and deal with the rest of method in the
     client-side, by handling failed POST,PUT,PATCH,etc. requests.
  */
  if (event.request.method !== 'GET') {
    /* If we don't block the event as shown below, then the request will go to
       the network as usual.
    */
    console.log('WORKER: fetch event ignored.', event.request.method, event.request.url);
    return;
  }
  /* Similar to event.waitUntil in that it blocks the fetch event on a promise.
     Fulfillment result will be used as the response, and rejection will end in a
     HTTP response indicating failure.
  */
  event.respondWith(
    caches
      /* This method returns a promise that resolves to a cache entry matching
         the request. Once the promise is settled, we can then provide a response
         to the fetch request.
      */
      .match(event.request)
      .then(function(cached) {
        /* Even if the response is in our cache, we go to the network as well.
           This pattern is known for producing "eventually fresh" responses,
           where we return cached responses immediately, and meanwhile pull
           a network response and store that in the cache.
           Read more:
           https://ponyfoo.com/articles/progressive-networking-serviceworker
        */
        var networked = fetch(event.request)
          // We handle the network request with success and failure scenarios.
          .then(fetchedFromNetwork, unableToResolve)
          // We should catch errors on the fetchedFromNetwork handler as well.
          .catch(unableToResolve);

        /* We return the cached response immediately if there is one, and fall
           back to waiting on the network as usual.
        */
        console.log('WORKER: fetch event', cached ? '(cached)' : '(network)', event.request.url);
        return cached || networked;

        function fetchedFromNetwork(response) {
          /* We copy the response before replying to the network request.
             This is the response that will be stored on the ServiceWorker cache.
          */
          var cacheCopy = response.clone();

          console.log('WORKER: fetch response from network.', event.request.url);

          caches
            // We open a cache to store the response for this request.
            .open(version + 'pages')
            .then(function add(cache) {
              /* We store the response for this request. It'll later become
                 available to caches.match(event.request) calls, when looking
                 for cached responses.
              */
              cache.put(event.request, cacheCopy);
            })
            .then(function() {
              console.log('WORKER: fetch response stored in cache.', event.request.url);
            });

          // Return the response so that the promise is settled in fulfillment.
          return response;
        }

        /* When this method is called, it means we were unable to produce a response
           from either the cache or the network. This is our opportunity to produce
           a meaningful response even when all else fails. It's the last chance, so
           you probably want to display a "Service Unavailable" view or a generic
           error response.
        */
        function unableToResolve () {
          /* There's a couple of things we can do here.
             - Test the Accept header and then return one of the `offlineFundamentals`
               e.g: `return caches.match('/some/cached/image.png')`
             - You should also consider the origin. It's easier to decide what
               "unavailable" means for requests against your origins than for requests
               against a third party, such as an ad provider
             - Generate a Response programmaticaly, as shown below, and return that
          */

          console.log('WORKER: fetch request failed in both cache and network.');

          /* Here we're creating a response programmatically. The first parameter is the
             response body, and the second one defines the options for the response.
          */
          return new Response('<h1>Service Unavailable</h1>', {
            status: 503,
            statusText: 'Service Unavailable',
            headers: new Headers({
              'Content-Type': 'text/html'
            })
          });
        }
      })
  );
});
```