# Studio Musique : l'app (page autonome)

Page web installable sur téléphone qui suit et pilote ton agent YouTube vers Soulseek et tes logiciels DJ.
Elle ne contient aucune donnée personnelle : tout passe par un dépôt GitHub **privé** que tu crées toi-même.

## Installation (une seule fois, environ 10 minutes)

### 1. Deux dépôts sur github.com
- `studio-musique-data` : **privé**. Il sert de boîte aux lettres entre le téléphone et le Mac (commandes, résultats, état).
- `studio-musique` : **public**. Il ne contient que cette page.

### 2. Un jeton d'accès
GitHub, Settings, Developer settings, Personal access tokens, **Fine-grained tokens**, Generate new token :
- Repository access : *Only select repositories*, puis **studio-musique-data** seulement.
- Permissions : *Contents* en **Read and write**. Rien d'autre.
- Crée-en un pour le Mac et un pour le téléphone : si l'un se perd, tu révoques juste celui-là.

### 3. Publier la page
Dans le Terminal du Mac :

    cd ~/youtube-playlist-agent/app
    git init -b main && git add -A && git commit -m "Studio Musique"
    git remote add origin https://github.com/TON-COMPTE/studio-musique.git
    git push -u origin main

Sur github.com, dépôt `studio-musique`, Settings, Pages : *Deploy from a branch*, branche `main`, dossier `/ (root)`.
Au bout d'une minute, la page est à `https://TON-COMPTE.github.io/studio-musique/`.

### 4. Brancher le Mac

    bash ~/youtube-playlist-agent/install/install_sync.sh TON-COMPTE/studio-musique-data

Le script demande le jeton du Mac (saisie masquée), teste la connexion et installe la passerelle
(elle tourne toute seule dès que le Mac est allumé). Il affiche aussi la commande de réveil nocturne (facultative).

### 5. Brancher le téléphone
Ouvre l'adresse de la page, puis « Ajouter à l'écran d'accueil ». Au premier lancement, colle
`TON-COMPTE/studio-musique-data` et le jeton du téléphone.

## Comment ça marche
- Le téléphone dépose une commande dans `queue/` (par exemple « ajouter ce lien YouTube »).
- Le Mac la récupère dans la minute s'il est allumé, la vérifie (liste d'actions autorisées, liens YouTube uniquement), l'exécute, puis renvoie le résultat dans `results/` et l'état dans `status.json`.
- Mac éteint : la commande attend dans la file et s'exécute à l'allumage. L'app affiche « Mac hors ligne depuis… ».

## Sécurité
- Le jeton reste dans le navigateur du téléphone et dans un fichier du Mac (droits 600, jamais commité).
- Même avec le jeton, on ne peut déclencher que les actions de la liste blanche : pas de commande libre.
- Le dépôt de données doit rester privé : `status.json` décrit ta bibliothèque. L'app refuse de se connecter à un dépôt public.
- Un jeton perdu : révoque-le dans GitHub (Settings, Developer settings), puis crée-en un nouveau.
