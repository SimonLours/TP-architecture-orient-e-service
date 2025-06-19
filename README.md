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

## Que se passe-t-il si deux clients POST /msg en même temps
Les deux requêtes peuvent arriver au même moment  
si la liste des messages est partagée sans verrou  
on peut perdre ou écraser un message  
il faut protéger cette zone critique avec un verrou

## Que faire si un pseudo est redéfini ou oublié
si redéfini on met à jour le pseudo en mémoire  
si oublié on retourne une erreur 400 ou on demande à l'utilisateur de se reconnecter

## Les requêtes GET /msg et POST /msg partagent-elles le même format
non  
POST /msg envoie un message au serveur  
GET /msg récupère une liste de messages  
les structures JSON sont différentes car les actions ne sont pas symétriques

## Que faudrait-il pour gérer plusieurs canaux
utiliser un dictionnaire où chaque canal a sa propre liste de messages  
chaque message contient son canal  
les routes pourraient être de type /canaux/<nom>/msg

-------------------------------------------------

## Limites

## Structure du code et organisation

- Un seul fichier devient vite illisible dès que l'API grandit  
- Mieux vaut séparer par thème : utilisateurs, messages, canaux  
- user_api.py ou message_api.py rend le code modulaire et clair  
- Pour tester juste les pseudos : extraire la logique dans un fichier et tester sans lancer tout le serveur  
- La doc peut être générée même si l’API est découpée, avec des outils comme Swagger ou Flask-RESTX

## Isolation des responsabilités

- Si la gestion des pseudos est isolée, on peut la modifier sans toucher au reste  
- L’authentification peut être réutilisée dans une autre app si elle est bien conçue  
- Certains composants comme utilisateurs et messages doivent parfois partager des données  
- On peut découper en services indépendants par domaine ou par équipe  
- Pour éviter un monolithe déguisé : éviter les dépendances croisées, limiter le couplage

## Déploiement et scalabilité

- On peut isoler la gestion des utilisateurs dans un service dédié sur un autre port  
- Déployer /msg sur deux serveurs permet de mieux gérer la charge et les pannes  
- Si le code est bien découpé on peut mettre à jour un composant sans redémarrer l’ensemble  
- L’API REST reste la même pour une app mobile, seule l’interface change  
- Outils utiles : Docker, Compose, Kubernetes

## Communication et cohérence

- Les services peuvent échanger via API REST, file de messages ou base commune  
- Si chaque service a sa mémoire, l’état partagé doit être stocké ailleurs ou synchronisé  
- Certaines données doivent être persistées dans une base centrale ou transmises par messages  
- Si un service tombe, les autres doivent savoir quoi faire : attente, retry ou mise en file  
- La cohérence globale est possible sans connaissance directe entre services si les contrats sont bien définis
"""


-------------------------------------------------


Simon Lours / Axel Pedrero
