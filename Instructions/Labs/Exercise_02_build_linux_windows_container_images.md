---
Exercise:
  title: "Exercice\_: Créer des images conteneur Linux et Windows et les stocker dans Azure Container Registry"
  module: Guided Project - Deploy applications to Azure Kubernetes Service
---
# Exercice 2 : Déployer des applications sur Azure Kubernetes Service (AKS)

## Objectifs

Ce projet guidé comprend les exercices suivants :

+ Exercice 1 : Provisionner Azure Container Registry (ACR) et Azure Kubernetes Service (AKS).
+ **Exercice 2 : Créer des images conteneur Linux et Windows et les stocker dans Azure Container Registry.**
+ Exercise 3 : Déployer des images conteneurs vers Azure Kubernetes Service.
+ Exercice 4 : Vérifier le déploiement et déprovisionner toutes les ressources.

Dans cet exercice, vous créez des images conteneur Linux et Windows et les stockez dans Azure Container Registry.

## Exercice 2 : Créer des images conteneur Linux et Windows et les stocker dans ACR
Dans cet exercice, vous créez des images Docker basées sur Linux et Windows, et vous les poussez dans le registre de conteneurs Azure que vous avez créé précédemment dans ce labo.


>**Remarque** : Pour faire cet exercice, vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free/).
> Pour toute propriété non spécifiée, utilisez la valeur par défaut.


### Tâche 1 : Créer une image conteneur Linux et la stocker dans ACR
Dans cette tâche, vous utilisez une tâche ACR pour créer une image conteneur Linux et la pousser automatiquement dans l’ACR.

1. Dans le portail Azure, sélectionnez l’icône **Cloud Shell**.
1. Si vous êtes invité à sélectionner **Bash** ou **PowerShell**, sélectionnez **Bash**. 
1. Si vous y êtes invité, sélectionnez **Créer un stockage** et attendez que le volet Azure Cloud Shell s’affiche. 
1. Vérifiez que **Bash** s’affiche dans le menu déroulant en haut à gauche du volet Cloud Shell.
1. Dans la session Bash dans Cloud Shell, créez un répertoire pour héberger le fichier Dockerfile de l’image Linux et accédez-y à partir du répertoire actuel en exécutant les commandes suivantes :

   ```bash
   mkdir ~/image-l01
   cd ~/image-l01
   ```

1. Dans la session Bash d’Azure Cloud Shell, utilisez l’éditeur intégré pour créer un fichier nommé server.js dans le répertoire image-l01 et copiez-y le contenu suivant :

   ```js
   const http = require('http')
   const port = 80
   const server = http.createServer((request, response) => {
     response.writeHead(200, {'Content-Type': 'text/plain'})
     response.write('Hello World from Node\n')
     response.end('Version: ' + process.env.NODE_VERSION + '\n')
   })
   server.listen(port)
   console.log(`Server running at http://localhost: ${port}`)
   ```

   > **Remarque :** Une fois exécuté, le code Node.js résultant affiche le message **Hello World from Node**.

1. Dans la session Bash d’Azure Cloud Shell, utilisez à nouveau l’éditeur intégré pour créer un fichier nommé package.json dans le répertoire image-l01 et copiez-y le contenu suivant :

   ```json
   {
     "name": "helloworld",
     "version": "1.0.0",
     "description": "Sample app for ACR Build",
     "main": "server.js",
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1",
       "start": "node server.js"
     },
     "license": "MIT"
   }
   ```

1. Enregistrez les changements du fichier et fermez-le pour revenir à l’invite Bash.
1. Dans la session Bash d’Azure Cloud Shell, utilisez à nouveau l’éditeur intégré pour créer un fichier nommé Dockerfile dans le répertoire image-l01 et copiez-y le contenu suivant :

   ```Dockerfile
   FROM node:20.2-alpine
   COPY . /src
   RUN cd /src && npm install
   EXPOSE 80
   CMD ["node", "/src/server.js"]
   ```

1. Enregistrez les changements du fichier et fermez-le pour revenir à l’invite Bash.
1. Dans la session Bash d’Azure Cloud Shell, identifiez le nom du registre de conteneurs Azure que vous avez créé précédemment dans cet exercice et stockez-le dans une variable nommée **$ACRNAME** en exécutant les commandes suivantes :

   ```azurecli
   ACR_RGNAME='acr-01-RG'
   ACR_NAME=$(az acr list --resource-group $ACR_RGNAME --query "[].name" --output tsv)
   ```

1. Dans la session Bash d’Azure Cloud Shell, créez une image Docker basée sur le fichier Dockerfile stocké dans le répertoire actuel et poussez-la automatiquement dans le registre de conteneurs Azure, dont le nom est stocké dans la variable **$ACRNAME**, en exécutant la commande suivante (n’oubliez pas le point à la fin) :

   ```azurecli
   az acr build --registry $ACR_NAME --image hellofromnode:v1.0 .
   ```

   > **Remarque :** Suivez la progression de la génération et vérifiez qu’elle se termine correctement. Cela devrait prendre moins d’une minute.

### Tâche 2 : Créer une image conteneur Windows et la stocker dans ACR
Dans cette tâche, vous utilisez une tâche ACR pour créer une image conteneur Windows et la pousser automatiquement dans l’ACR.

1. Dans la session Bash dans Cloud Shell, créez un répertoire pour héberger le fichier Dockerfile de l’image Windows et accédez-y à partir du répertoire actuel en exécutant les commandes suivantes :

   ```bash
   mkdir ~/image-w01
   cd ~/image-w01
   ```

1. Dans la session Bash d’Azure Cloud Shell, clonez un dépôt GitHub public hébergeant les fichiers que vous utilisez pour générer l’image Windows et basculez le répertoire actuel vers le dépôt cloné en exécutant les commandes suivantes :

   ```git
   git clone https://github.com/Azure-Samples/dotnetcore-docs-hello-world.git
   cd ~/image-w01/dotnetcore-docs-hello-world
   ```

   > **Remarque :** Le dépôt contient le code source d’une application web .NET 7 qui affiche le message **Hello World from .NET 7**.

1. Dans la session Bash d’Azure Cloud Shell, créez une image Docker basée sur le fichier Dockerfile stocké dans le répertoire actuel et poussez-la automatiquement dans le registre de conteneurs Azure, dont le nom est stocké dans la variable **$ACRNAME**, en exécutant la commande suivante (n’oubliez pas le point à la fin) :

   ```azurecli
   az acr build --registry $ACR_NAME --image hellofromdotnet:v1.0 --platform windows --file Dockerfile.windows .
   ```

   > **Remarque :** Le nom du fichier Dockerfile définissant la génération d’image Windows est **Dockerfile.windows**.

   > **Remarque :** Suivez la progression de la génération et vérifiez qu’elle se termine correctement. L’opération doit durer moins de 3 minutes.

1. Fermez le volet Azure Cloud Shell.
1. Dans le portail Azure, accédez à la page **Registres de conteneurs** et sélectionnez l’entrée représentant le registre de conteneurs dans lequel vous avez poussé les deux images.
1. Dans la page Registre de conteneurs, dans le menu hub vertical, sélectionnez **Dépôts** et vérifiez que **hellofromnode** et **hellofromdotnet** s’affichent dans la liste des dépôts.
