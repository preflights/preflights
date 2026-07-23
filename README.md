Modèle de page Confluence : Architecture Générale AS IS - Îlot [Nom de l'Îlot]
1. Raison d'être de l'Îlot (AS IS)
Périmètre actuel : À quoi sert concrètement cet adapter aujourd'hui ? (ex: "Permet au front-end de lire les comptes du CBS").

Cas d'usage métiers supportés (Use Cases) :

Exemple : Ouverture de compte simple.

Exemple : Lecture de l'historique.

2. Modèle Mental & Concepts Métiers Actuels
Modèle de données exposé : Comment les concepts du CBS sont-ils traduits actuellement par l'adapter ? (ex: Titulaires, Comptes, Transactions).

Règles de gestion spécifiques implémentées : (ex: Comment la différence entre Booked et Non-booked est-elle gérée aujourd'hui ? Est-ce que l'adapter fait le calcul lui-même ou redescend-il la donnée brute du CBS ?).

3. Fonctionnement de l'Intégration (Existant)
Pour chaque grand cas d'usage actuel, documenter comment l'intégration fonctionne en ce moment.

[Nom du Cas d'Usage, ex: Création d'un client] :

Séquence actuelle : (Diagramme montrant comment l'adapter interroge le CBS).

Payload actuel : (Exemple du JSON tel qu'il est envoyé/reçu aujourd'hui).

4. Grille d'Audit des Capacités Opérationnelles
Cette section évalue les standards d'intégration de l'API. (Indiquer le mécanisme existant, ou inscrire "Non géré" / "Absent").

Traçabilité & Observabilité : (Comment suit-on une requête aujourd'hui entre le consommateur, l'adapter et le CBS ? Y a-t-il un ID de corrélation ?)

Idempotence : (Comment l'API gère-t-elle les doubles appels front-end actuellement ?)

Gestion des Erreurs : (Les erreurs sont-elles standardisées ? L'adapter renvoie-t-il des codes techniques du CBS ou des messages compréhensibles ?)

Comportement Asynchrone : (L'adapter publie-t-il des événements métier ? Y a-t-il des Webhooks ?)

Protection du CBS (Rate Limiting / Caching) : (Qu'est-ce qui empêche le front-end de surcharger le CBS via l'adapter aujourd'hui ?)

Bouchonnage (Sandbox) : (Comment les consommateurs testent-ils les cas d'erreur rares en UAT ?)

5. Références Techniques
Lien vers la spécification OpenAPI / Swagger actuelle générée par le code.

URLs des environnements actuels (DEV, UAT, PROD).

Mécanisme d'authentification utilisé.
