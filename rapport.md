Backend:

Générer un fichier package-lock.json. parce que les dépendances peuvent changer et pour s'assurer qu'on utilise les version dans package json.

On a choisi une image lightweight de node pour construire notre image.

Rajouter .dockerignore pour ignorer les node modules etc.

Dockerfile:

Copie les deux fichiers dans /app :
- package.json : décrit les dépendances du projet.
- package-lock.json : contient les versions exactes des dépendances.
Pourquoi les copier séparément du reste du code ?

Pour profiter du cache Docker.

Si on modifie uniquement server.js, Docker peut réutiliser la couche contenant les dépendances et éviter de les réinstaller.

Ligne 5 — Installer les dépendances
RUN npm ci

Installe toutes les dépendances définies dans package-lock.json.

Contrairement à ton Dockerfile de production, il n'y a pas --omit=dev.

Cela signifie que npm installe :
- Les dépendances de production (dependencies).
- Les dépendances de développement (devDependencies).

Ligne 6 — Copier le code source
COPY --chown=node:node server.js ./

Copie server.js dans /app.

--chown=node:node attribue le fichier à l'utilisateur node et au groupe node.

Cela évite que le fichier appartienne à root.
Ligne 7 — Utilisateur non-root
USER node

Indique que les prochaines instructions et le processus lancé au démarrage utiliseront l'utilisateur node.

Par défaut, les conteneurs exécutent souvent leurs processus en tant que root.

Utiliser un utilisateur non privilégié limite les conséquences d'une éventuelle compromission de l'application.

C'est une bonne pratique, même en développement.
Ligne 8 — Exposer le port
EXPOSE 3007

Indique que l'application écoute sur le port 3007.


Ligne 9 — Commande de démarrage
CMD ["npm", "run", "dev"]

Indique la commande à exécuter lorsque le conteneur démarre.

npm run dev exécute le script dev défini dans le package.json.

Par exemple :
{
  "scripts": {
    "dev": "nodemon server.js"
  }
}

Dans ce cas :
- npm démarre le script dev.
- Nodemon lance server.js.
- Nodemon surveille les modifications du code.
- Si on modifies server.js, le serveur redémarre automatiquement.


Alpine met à jour les paquets concernés.

Pourquoi ?

Pour intégrer les correctifs de sécurité disponibles et réduire les vulnérabilités présentes dans les paquets système.
Cette commande met à jour les paquets installés dans ton système Alpine Linux.

Database:

On a la config du nginx qui fonctionne en tant que reverse proxy.