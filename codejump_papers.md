## Papers
===

*Plop ! petit papier pour mettre toute mes idées/recherches/tests à plat, si vous voulez participer n'hésitez pas ! :)*




### HeXHTTP

Plusieurs petits tests dans le tool HeXHTTP tel que:

#### Header ``` Range: bytes=nobytes```

*Pour résumé: 
C'est la possibilité de récupérer en HTTP, non pas la totalité d'une ressource mais une partie, un intervalle (range) de celle-ci. Cette fonction est utile, par exemple, lorsqu'un transfert est interrompu en cours de route et qu'on voudrait pouvoir reprendre le téléchargement au point où il s'était arrêté, et non pas bêtement depuis le début.*

Donc si nous envoyons une requête tel que ```curl http://i.imgur.com/z4d4kWk.jpg -i -H "Range: bytes=0-1023"``` nous aurons la réponse du premier au 1023 bytes.

Mais si nous envoyons un "range" qui n'existe pas, que le serveur ne comprends pas tel que ```"Range: bytes=-10"``` ou simplement ```"Range: bytes=plop"``` celui-ci nous renvois (dans la plupart des cas) une réponse "416" (Range Not Satisfiable).

##### GOAL

Le but ici va donc être de voir si il est possible d'avoir des erreurs un peu trop "permissives" lors de cette requête ou encore d'essayer de la mettre en cache afin d'effectuer du "cache poisoning" via celle-ci.

##### Exemples



##### Ressources

- https://developer.mozilla.org/en-US/docs/Web/HTTP/Range_requests


#### L'évolution du protocole HTTP

En lisant un peu l'évolution du protocol HTTP nous pouvons vor qu'il en a exister plusieurs:

- HTTP/0.9: *La version initiale de HTTP n'avait pas de numéro de version. Elle fut appelée 0.9 pour la différencier des versions ultérieures. HTTP/0.9 est extrêmement simple : la requête se compose d'une ligne unique et commence par la seule méthode possible GET, suivie par le chemin pour accéder à la ressource (sans l'URL, puisque ni protocole, serveur ni port ne sont nécessaires quand on est connecté au serveur)*
- HTTP/1.0: *HTTP/0.9 était très limité. Navigateurs et serveurs l'ont rapidement étendu vers des usages plus polyvalents.*
	- Dans chaque requête figurent dorénavant les informations de version (HTTP/1.0 est ajouté à la ligne GET).
	- Une ligne de code d'état est aussi envoyée au début de chaque réponse. Elle permet au navigateur de prendre connaissance du succès ou de l'échec de la requête, et de s'adapter en conséquence (avec une mise à jour, par exemple, ou en utilisant son cache local de manière spécifique).
	- La notion d'en-tête HTTP a été mise en place à la fois pour les requêtes et pour les réponses. Elle autorise la transmission de métadonnées, et rend le protocole très flexible et extensible.
    - Avec ces nouveaux en-têtes HTTP, il est désormais possible de transmettre d'autres documents que des fichiers HTML bruts (grâce à l'en-tête Content-Type.
- HTTP/1.1: *HTTP/1.1 dissipait des ambiguïtés et introduisait de nombreuses améliorations.*
	- Connexion pouvant être ré-utilisée : économie du temps qu'il faudrait pour en ouvrir plusieurs dans le but de présenter les ressources constituant le document original récupéré.
    - Ajout du pipelining : permet d'envoyer une seconde requête avant que la réponse de la première ne soit complètement transmise, diminuant le temps de latence de la communication.
    - Désormais les réponses par morceau sont aussi supportées.
    - Mise en place de mécanismes de contrôle de caches additionnels.
    - Mise en place de la négociation de contenu pour le langage, l'encodage et le type : le client et le serveur peuvent ainsi se mettre d'accord sur le contenu le plus adéquat à échanger.
    - Grâce à l'en-tête Host, la capacité à héberger différents domaines sur la même adresse IP autorise désormais une colocation de serveurs.
- HTTP/2.0: *Le protocole HTTP/2 diffère de HTTP/1.1 sur plusieurs aspects*
	- Il est encodé en binaire plutôt qu'en texte. Il ne peut donc plus être lu ou écrit à la main. Malgré cette difficulté, il est désormais possible d'implémenter des techniques d'optimisation avancée.
    - C'est un protocole multiplexé. Plusieurs requêtes en parallèle peuvent être gérées au sein de la même connexion, supprimant ainsi la limitation séquentielle de HTTP/1.x.
    - HTTP/2 compresse les en-têtes, étant donné que des en-têtes similaires sont échangés lors d'une suite de requêtes, on supprime ainsi la duplication et l'échange inutiles des données similaires.
    - Il permet au serveur de remplir le cache du client avant qu'il ne soit demandé par ce dernier, on parle alors d'évènements générés par le serveur.

Bref, je me suis donc posé la question, si je testais de forcer les différents protocole lors d'une requête, obtiendrai je des résultats différents ?

Spoiler: pour l'instant j'ai rien trouvé d'interressant sur le sujet, plusieurs erreur lors du HTTP/0.9 ainsi que du 2.0 mais pas d'erreur louche ou autre. Peux-être un jour ne sait-on jamais :)


##### Exemples
```
└── HTTP/0.9 : 200 [253448 bytes] [HS: 18b]
└── HTTP/1.0 : 200 [253448 bytes] [HS: 18b]
└── HTTP/1.1 : 200 [253448 bytes] [HS: 18b]
└── HTTP/2   : 400 [222 bytes]   [HS: 17b]
```

##### Ressources

- https://developer.mozilla.org/fr/docs/Web/HTTP/Basics_of_HTTP/Evolution_of_HTTP