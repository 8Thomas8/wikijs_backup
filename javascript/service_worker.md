---
title: PWA: Le Service Worker
description: 
published: true
date: 2020-08-18T10:20:40.738Z
tags: javascript, pwa, service worker
editor: markdown
---

# PWA: LE SERVICE WORKER

Ce qui est important à gérer c'est la gestion du cache.
L'autre chose très importante est la stratégie d'accès aux requetes:
- Cache first
- Network first
- Race cache/network (le premier dispo sert le résultat)

## Exemple service worker.
Stratégie network first home made
```javascript
// DEBUG mode
const DEBUG_INSTALL = false;
const DEBUG_ACTIVATE = false;
const DEBUG_FETCH = true;
const DEBUG_FETCH_NAV_IMG = false;

// La VERSION permet de gérer la gestion du cache en fonction de la VERSION de Service Worker.
const VERSION = 'v0:0:1';
const CACHE_NAME = 'nomducache';

// Path des fichiers statics à mettre en cache.
let urlsToCache = [
    '/',
    '/page1',
    '/page2',
    '/page3'
];

// Mise en cache des assets s'ils ne le sont pas déjà.
const cacheAsset = (url) => {
    return caches.open(VERSION + CACHE_NAME)
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

    DEBUG_INSTALL && console.group('SW : Install')
    DEBUG_INSTALL && console.log("Installation en cours.");

    event.waitUntil(
        fetch('./build/front/manifest.json').then(resp => {
            resp.json()
                .then((json) => {
                    DEBUG_INSTALL && console.log('Récupération des urls de fichier versionnés à cacher depuis' +
                        ' le manifest.json du dossier front/');
                    Object.keys(json).forEach(key => {
                        if (json[key].includes('css') || json[key].includes('js')) {
                            filesUrl.push(json[key]);
                        }
                    })
                    Promise.resolve(filesUrl).then(() => {
                        urlsToCache = urlsToCache.concat(filesUrl);
                    });
                }).then(() => {
                DEBUG_INSTALL && console.log('Mise en cache des fichiers statics + fichiers versionnés.');
                DEBUG_INSTALL && console.groupEnd();

                Promise.all(
                    urlsToCache.map(url => cacheAsset(url)),
                    self.skipWaiting()
                )
            });
        })
    )
});

// Activation du service worker.
self.addEventListener("activate", event => {
    DEBUG_ACTIVATE && console.group('SW : Activate')
    DEBUG_ACTIVATE && console.log("Activation en cours.");

    // Suppression du cache si il ne correspond pas à la VERSION du SW.
    event.waitUntil(
        caches
            .keys()
            .then(keys => {
                return Promise.all(
                    keys
                        .filter(key => {
                            return !key.startsWith(VERSION)
                        })
                        .map(key => {
                            return caches.delete(key)
                        })
                );
            })
            .then(() => {
                DEBUG_ACTIVATE && console.log('Activé');
                DEBUG_ACTIVATE && console.groupEnd();
            })
    );
});

// Interception des requêtes et application de la stratégie network first.
self.addEventListener("fetch", event => {

    const req = event.request;

    // Ignore certaines requêtes:
    // Vérifie si les requêtes ne sont pas dans urlToCache et si c'est le cas, si elles ne sont pas en mode navigate.
    if (!urlsToCache.includes(req.url.replace('https://' + self.location.hostname, '')) && req.mode !== 'navigate') {
        // Vérifie si les requêtes ne sont pas des images png, svg, jpg, gif.
        if (!(req.url.endsWith('.png') || req.url.endsWith('.svg')
            || req.url.endsWith('.jpg') || req.url.endsWith('.gif'))) {
            DEBUG_FETCH_NAV_IMG && console.log('SW Fetch : Récupération de la requête abandonné ( mode !== navigate et ce n\'est pas une image )', req.url);
            // Ignore la requête.
            return;
        }
    }

    event.respondWith(
        fetch(req)
            .then(
                // Si réponse du network.
                serveFromNetwork()
            )
            .catch(serveFromCache)
    )

    function serveFromNetwork() {
        return response => {
            // Clone la réponse pour l'utiliser une seconde fois.
            let cacheCopy = response.clone();

            // Enregistre la réponse clonée dans la cache.
            caches
                .open(VERSION + CACHE_NAME)
                .then(
                    cache => {
                        cache.put(req, cacheCopy);
                    }
                )
                .then(() => {
                    DEBUG_FETCH && console.group('SW Fetch serveFromNetwork() : Requête', req.url);
                    DEBUG_FETCH && console.log('Réponse récupérée online.');
                    DEBUG_FETCH && console.log('Réponse online ajouté au cache.');
                    DEBUG_FETCH && console.groupEnd();
                });

            // Sert la réponse obtenu online.
            return response;
        };
    }

    function serveFromCache() {
        return caches.match(req)
            .then(
                // Réponse du cache.
                response => {
                    DEBUG_FETCH && console.group('SW Fetch serveFromCache() : Requête', req.url);
                    DEBUG_FETCH && console.log('Impossible de récupérer la réponse online.', req.url);

                    if (response) {
                        DEBUG_FETCH && console.log('Récupération de la réponse depuis le cache');
                        DEBUG_FETCH && console.groupEnd()
                        return response;
                    } else {
                        DEBUG_FETCH && console.log('Impossible de récupérer la réponse depuis le cache');
                        return unableToResolve();
                    }
                })
            .catch(() => {
                DEBUG_FETCH && console.log('Impossible de récupérer la réponse depuis online ou le cache');
                return unableToResolve();
            })
    }

    // Retourne une réponse pour les requêtes impossibles à joindre
    function unableToResolve() {
        DEBUG_FETCH && console.log('unableToResolve() déclenchée - erreur 503.')
        DEBUG_FETCH && console.groupEnd();
        return new Response(
            '<h1 style="text-align: center;">Service indisponible</h1>', {
                status: 503,
                statusText: 'Service Unavailable',
                headers: new Headers({
                    'Content-Type': 'text/html'
                })
            });
    }
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

## Code utile pour le JS du site

```javascript
/*---------------------------------------------------------------------
----------------------------- PWA -----------------------------------
---------------------------------------------------------------------*/
// Gestion du service worker et du block d'installation de la PWA.
let showInstallPwa = true;

// Lance l'enregistrement du service worker une fois le site chargé.
window.addEventListener('load', e => {
    if (mobileCheck(true)) {
        swRegister();
    }
});

// Vérification si navigateur mobile ou non. Retourne true si le navigateur est un navigateur mobile.
// Force la valeur de retour en passant true en paramètre de la fonction.
function mobileCheck(force = false) {
    let check = false;
    (function (a) {
        if (/(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows ce|xda|xiino/i.test(a) || /1207|6310|6590|3gso|4thp|50[1-6]i|770s|802s|a wa|abac|ac(er|oo|s\-)|ai(ko|rn)|al(av|ca|co)|amoi|an(ex|ny|yw)|aptu|ar(ch|go)|as(te|us)|attw|au(di|\-m|r |s )|avan|be(ck|ll|nq)|bi(lb|rd)|bl(ac|az)|br(e|v)w|bumb|bw\-(n|u)|c55\/|capi|ccwa|cdm\-|cell|chtm|cldc|cmd\-|co(mp|nd)|craw|da(it|ll|ng)|dbte|dc\-s|devi|dica|dmob|do(c|p)o|ds(12|\-d)|el(49|ai)|em(l2|ul)|er(ic|k0)|esl8|ez([4-7]0|os|wa|ze)|fetc|fly(\-|_)|g1 u|g560|gene|gf\-5|g\-mo|go(\.w|od)|gr(ad|un)|haie|hcit|hd\-(m|p|t)|hei\-|hi(pt|ta)|hp( i|ip)|hs\-c|ht(c(\-| |_|a|g|p|s|t)|tp)|hu(aw|tc)|i\-(20|go|ma)|i230|iac( |\-|\/)|ibro|idea|ig01|ikom|im1k|inno|ipaq|iris|ja(t|v)a|jbro|jemu|jigs|kddi|keji|kgt( |\/)|klon|kpt |kwc\-|kyo(c|k)|le(no|xi)|lg( g|\/(k|l|u)|50|54|\-[a-w])|libw|lynx|m1\-w|m3ga|m50\/|ma(te|ui|xo)|mc(01|21|ca)|m\-cr|me(rc|ri)|mi(o8|oa|ts)|mmef|mo(01|02|bi|de|do|t(\-| |o|v)|zz)|mt(50|p1|v )|mwbp|mywa|n10[0-2]|n20[2-3]|n30(0|2)|n50(0|2|5)|n7(0(0|1)|10)|ne((c|m)\-|on|tf|wf|wg|wt)|nok(6|i)|nzph|o2im|op(ti|wv)|oran|owg1|p800|pan(a|d|t)|pdxg|pg(13|\-([1-8]|c))|phil|pire|pl(ay|uc)|pn\-2|po(ck|rt|se)|prox|psio|pt\-g|qa\-a|qc(07|12|21|32|60|\-[2-7]|i\-)|qtek|r380|r600|raks|rim9|ro(ve|zo)|s55\/|sa(ge|ma|mm|ms|ny|va)|sc(01|h\-|oo|p\-)|sdk\/|se(c(\-|0|1)|47|mc|nd|ri)|sgh\-|shar|sie(\-|m)|sk\-0|sl(45|id)|sm(al|ar|b3|it|t5)|so(ft|ny)|sp(01|h\-|v\-|v )|sy(01|mb)|t2(18|50)|t6(00|10|18)|ta(gt|lk)|tcl\-|tdg\-|tel(i|m)|tim\-|t\-mo|to(pl|sh)|ts(70|m\-|m3|m5)|tx\-9|up(\.b|g1|si)|utst|v400|v750|veri|vi(rg|te)|vk(40|5[0-3]|\-v)|vm40|voda|vulc|vx(52|53|60|61|70|80|81|83|85|98)|w3c(\-| )|webc|whit|wi(g |nc|nw)|wmlb|wonu|x700|yas\-|your|zeto|zte\-/i.test(a.substr(0, 4))) check = true;
    })(navigator.userAgent || navigator.vendor || window.opera);
    return force ? force : check;
}

// Regarde si le navigateur est compatible avec les SW et lance appel le fichier.
function swRegister() {
    if ('serviceWorker' in navigator) {
        navigator.serviceWorker
            .register('sw.js')
            .then(() => {
                console.log('SW: Installation terminée');
            }).catch((error) => {
            console.log('SW: Erreur d\'installation : ' + error);
        });
    }
}

// Déclaration stockage de l'event d'installation de la PWA
let deferredPrompt;

// Récupération de l'event prompt de l'installation.
window.addEventListener('beforeinstallprompt', (e) => {
    e.preventDefault();

    //On récupère l’évènement pour l'utiliser plus tard.
    deferredPrompt = e;

    // On lance la gestion de l'affichage du block d'installation de la PWA.
    manageInstallBlock(showInstallPwa, observer);
});

// Installation au click sur le bouton #btnInstall.
document.getElementById("btnInstall").addEventListener('click', (e) => {
    // Affiche la demande d’installation
    deferredPrompt.prompt();

    deferredPrompt.userChoice.then((choiceResult) => {
        if (choiceResult.outcome === 'accepted') {
            console.log('PWA: Installation réussie');
        } else {
            console.log('PWA: Installation refusée');
        }
        deferredPrompt = null;

        // On ferme le block d'installation de la PWA.
        document.getElementById('installBlock').style.transform = 'translateY(60px)';
        showInstallPwa = false;
        manageInstallBlock(showInstallPwa, observer);
    });
});
```