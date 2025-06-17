# TP-architecture-orientee-service Simon Lours / Axel Pedrero

## Contexte

### 3.1 Session, état, communication

**1.** en tcp chaque client garde sa connexion donc c’est simple de suivre son état mais en http comme c’est stateless faut tout renvoyer à chaque fois  
du coup faut redonner les infos à chaque requête comme le pseudo ou utiliser une solution comme un token ou un cookie pour garder un peu d’état

**2.** oui on peut redemander le pseudo à chaque fois dans le json de la requête mais c’est pas hyper pratique  
du coup mieux d’utiliser un cookie ou un token d’authentification pour lier les requêtes à un utilisateur  
niveau sécurité faut faire gaffe au vol de cookie ou au token exposé et pour l’ergonomie c’est plus fluide si le client n’a pas à tout retaper

**3.** http peut pas pousser des messages comme en tcp
il y a le polling où le client demande toutes les x secondes  
ou le long-polling où il reste en attente jusqu’à un nouveau message  
Il y a aussi les websockets mais c’est plus complexe et hors du modèle rest classique

### 3.2 Ressources, verbes, structure

**4.** oui une commande comme /join peut devenir un POST sur /join avec un body comme { "canal": "pause_cafe" }  
ça dépend comment on modélise mais l’idée c’est que ça devienne une action sur une ressource

**5.** GET /msg peut retourner tous les messages mais c’est pas optimal  
mieux de filtrer par canal genre GET /canal/general/messages  
et l’ordre des messages c’est important donc faut les renvoyer du plus ancien au plus récent  
la logique métier genre vérifier que le user est bien dans le canal peut être dans le contrôleur

**6.** les urls peuvent suivre une hiérarchie claire genre /canaux puis /canaux/general puis /canaux/general/messages  
ça aide à s’y retrouver et à structurer proprement  
faut garder des noms simples en minuscule avec des tirets ou underscores si besoin

### 3.3 Robustesse, concurrence, évolutivité

**7.** si deux users postent en même temps faut un mécanisme pour garder l’ordre comme un verrou sur la ressource partagée ou une file d’attente  
sinon il y a risque de condition de course et messages mal classés

**8.** garder l’état en mémoire c’est bien pour démarrer mais ça tient pas quand il y a plusieurs instances ou en cas de redémarrage  
faut une base de données ou un système de persistance pour assurer que les infos soient conservées

**9.** comme chaque requête http ouvre et ferme une connexion ça peut vite charger le réseau et le cpu  



-------------------------------------------------

## Fonctions


-------------------------------------------------

## Limites




-------------------------------------------------


Simon Lours / Axel Pedrero
