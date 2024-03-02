---
Guided Exercise:
  title: Exercice guidé Déployer des applications sur AKS
---
# Exercice guidé : Déployer des applications sur Azure Kubernetes Service (AKS)

## Objectifs

Cet exercice guidé comprend les activités suivantes :

+ Exercice 1 : Provisionner Azure Container Registry (ACR) et Azure Kubernetes Service (AKS)
+ Exercice 2 : Créer des images conteneur Linux et Windows, puis les stocker dans ACR
+ Exercice 3 : Déployer des images conteneur sur AKS 
+ Exercice 4 : Évaluer le déploiement et déprovisionner toutes les ressources

## Exercice 1 : Provisionner Azure Container Registry (ACR) et Azure Kubernetes Service (AKS)
Dans cet exercice, vous créez un registre de conteneurs Azure et un cluster AKS.


>**Remarque** : Pour faire cet exercice, vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free/).
> Pour toute propriété non spécifiée, utilisez la valeur par défaut.


### Tâche 1 : Créer un registre de conteneurs Azure
Dans cette tâche, vous créez un registre de conteneurs Azure

1. À partir de votre ordinateur, ouvrez une fenêtre de navigateur web et accédez au Portail Azure à l’adresse https://portal.azure.com.
1. Quand vous y êtes invité, connectez-vous en utilisant un compte d’utilisateur avec le rôle Propriétaire dans l’abonnement Azure que vous utilisez dans cet exercice. 
1. Connectez-vous au portail Azure. 
1. Dans le portail Azure, dans la zone de texte **Rechercher**, recherchez et sélectionnez **Registres de conteneurs**.
1. Dans la page **Registres de conteneurs**, sélectionnez **+ Créer** et spécifiez les paramètres suivants :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|Nom de l’abonnement Azure que vous utiliserez dans cet exercice|
    |Groupe de ressources|Nom d’un nouveau groupe de ressources **acr-01-RG**|
    |Nom du registre|Nom globalement unique valide de 5 à 50 caractères alphanumériques|
    |Région|Région Azure dans laquelle vous pouvez créer un registre de conteneurs Azure et un cluster AKS|
    |Zones de disponibilité|**Aucun**|
    |SKU|**De base**|

1. Dans la page **Registres de conteneurs**, sélectionnez **Vérifier + créer** et, sous l’onglet **Vérifier + créer**, sélectionnez **Créer**.

   > **Remarque :** Passez à l’exercice suivant sans attendre la fin du provisionnement d’Azure Container Registry.

### Tâche 2 : Créer un réseau virtuel Azure et un cluster AKS
Dans cette tâche, vous créez un réseau virtuel Azure et déployez un cluster AKS comprenant un pool de nœuds Windows dans ce réseau virtuel.

> **Remarque :** Même si vous pouvez créer un réseau virtuel pendant le provisionnement d’un cluster AKS, il ne s’agit pas d’un scénario classique. Par ailleurs, le déploiement de clusters AKS dans un réseau virtuel existant nécessite des considérations supplémentaires que vous devez connaître.

1. Dans le portail Azure, dans la zone de texte **Rechercher**, recherchez et sélectionnez **Réseaux virtuels**.
1. Dans la page **Réseaux virtuels**, sélectionnez **+ Créer**, puis, sous l’onglet **Informations de base** de la page **Créer un réseau virtuel**, spécifiez les paramètres suivants :

   |Paramètre|Valeur|
   |---|---|
   |Abonnement|Nom de l’abonnement Azure que vous avez sélectionné dans le premier exercice|
   |Groupe de ressources|Nom d’un nouveau groupe de ressources **aks-01-RG**|
   |Nom du réseau virtuel|**vnet-01**|
   |Région|Même région Azure que vous avez sélectionnée dans le premier exercice de cet exercice|

1. Sous l’onglet **Informations de base** de la page **Créer un réseau virtuel**, sélectionnez **Suivant**.
1. Sous l’onglet **Sécurité** de la page **Créer un réseau virtuel**, acceptez les paramètres par défaut et sélectionnez **Suivant**.
1. Sous l’onglet **Adresses IP** de la page **Créer un réseau virtuel**, vérifiez que l’espace d’adressage IP est défini sur **10.0.0.0/16**, supprimez le sous-réseau **par défaut**, sélectionnez **Vérifier + créer** et, sous l’onglet **Vérifier + créer**, sélectionnez **Créer**.

   > **Remarque :** La création d’un réseau virtuel devant durer seulement quelques secondes, vous devez pouvoir passer directement à l’étape suivante.

1. Dans le portail Azure, dans la zone de texte **Rechercher**, recherchez et sélectionnez **Services Kubernetes**.
1. Dans la page **Services Kubernetes**, sélectionnez **+ Créer**, dans la liste déroulante, sélectionnez **Créer un cluster Kubernetes**, puis, sous l’onglet **Informations de base** de la page **Créer un cluster Kubernetes**, spécifiez les paramètres suivants et sélectionnez **Suivant** :

   |Paramètre|Valeur|
   |---|---|
   |Abonnement|Nom de l’abonnement Azure que vous avez sélectionné dans le premier exercice de ce labo|
   |Groupe de ressources|**aks-01-RG**|
   |Configuration prédéfinie du cluster|**Dev/Test**|
   |Nom du cluster Kubernetes|**aks-01**|
   |Région|Même région Azure que vous avez sélectionnée dans le premier exercice de ce labo|
   |Zones de disponibilité|**Aucun**|
   |Niveau tarifaire AKS|**Gratuit**|
   |Version de Kubernetes|Accepter la valeur par défaut|
   |Mise à niveau automatique|Désactivé|
   |Type de canal de sécurité du nœud|**Aucun**|
   |Authentification et autorisation|**Comptes locaux avec RBAC Kubernetes**|

1. Sous l’onglet **Pools de nœuds** de la page **Créer un cluster Kubernetes**, effectuez les tâches suivantes :

   - Dans la section **Pools de nœuds**, sélectionnez le lien ** agentpool**.
   - Dans la page **Mettre à jour le pool de nœuds**, dans la section **Taille de nœud**, sélectionnez le lien **Choisir une taille**.
   - Dans la page **Sélectionner une taille de machine virtuelle**, dans la liste des tailles de machine virtuelle, sélectionnez **B4ms**, puis cliquez sur **Sélectionner**.
   - De retour dans la page **Mettre à jour le pool de nœuds**, définissez **Méthode de mise à l’échelle** sur **Manuelle** et **Nombre de nœuds** sur **2**.
   - Dans la page **Mettre à jour le pool de nœuds**, sélectionnez **Mettre à jour**.

   > **Remarque :** Vous devez peut-être augmenter les quotas de processeur virtuel ou changer la référence SKU VM pour prendre en charge la taille de nœud et le nombre de nœuds. Pour plus d’informations sur la procédure d’augmentation des quotas de processeurs virtuels, consultez l’article Microsoft Learn [Augmenter les quotas de processeurs virtuels d’une famille VM](https://learn.microsoft.com/en-us/azure/quotas/per-vm-quota-requests).

   > **Remarque :** Vous ajoutez un pool de nœuds Windows au cluster. Cela nécessite de remplacer la configuration réseau par défaut **Kubenet** par **Azure CNI**. La configuration réseau Kubenet ne prend pas en charge les pools de nœuds Windows.

1. De retour sous l’onglet **Pools de nœuds** de la page **Créer un cluster Kubernetes**, sélectionnez **Suivant**.
1. Sous l’onglet **Mise en réseau** de la page **Créer un cluster Kubernetes**, sélectionnez l’option **Azure CNI**. Cochez la case **Apporter votre propre réseau virtuel**, puis sélectionnez **vnet-01** dans la liste déroulante **Réseau virtuel**. Sous la zone de texte **Sous-réseau du cluster**, sélectionnez **Gérer la configuration du sous-réseau**.
1. Dans la page **vnet-01 \| Sous-réseaux**, sélectionnez **+ Sous-réseau**.
1. Dans la page **Ajouter des sous-réseaux**, spécifiez les paramètres suivants et sélectionnez **Enregistrer** :

   |Paramètre|Valeur|
   |---|---|
   |Nom|**aks-subnet**|
   |Plage d’adresses de sous-réseau|**10.0.0.0/20**|

1. De retour sur la page **vnet-01 \| Sous-réseaux**, dans la barre de navigation en haut à gauche de la page, sélectionnez **Créer un cluster Kubernetes**. 
1. De retour sous l’onglet **Réseau** de la page **Créer un cluster Kubernetes**, spécifiez les paramètres suivants :

   |Paramètre|Valeur|
   |---|---|
   |Réseau virtuel|**vnet-01**|
   |Sous-réseau de cluster|**aks-subnet (10.0.0.0/20)**|
   |Plage d’adresses de service Kubernetes|**172.16.0.0/22**|
   |Adresse IP du service DNS Kubernetes|**172.16.3.254**|
   |Préfixe du nom DNS|**aks-01-dns**|
   |Stratégie réseau|**Aucun**|

1. Sous l’onglet **Mise en réseau** de la page **Créer un cluster Kubernetes**, sélectionnez **Précédent**.
1. De retour sous l’onglet **Pools de nœuds** de la page **Créer un cluster Kubernetes**, sélectionnez **+ Ajouter un pool de nœuds**.
1. Dans la page **Ajouter un pool de nœuds**, spécifiez les paramètres suivants :

   |Paramètre|Valeur|
   |---|---|
   |Nom du pool de nœuds|**w1pool**|
   |Mode|**Utilisateur**|
   |Type de système d’exploitation|**Windows 2022**|
   |Zone de disponibilité|**Aucun**|
   |Activer les instances Azure Spot|Désactivé|
   |Taille du nœud|**B4ms**|
   |Méthode de mise à l’échelle|**Manuel**|
   |Nombre de nœuds|**2**|
   |Pods maximum par nœud|**30**|
   |Activer l’IP publique par nœud|Désactivé|

   > **Remarque :** Ici aussi, vous devrez probablement augmenter les quotas de processeur virtuel ou changer la référence SKU de machine virtuelle pour prendre en charge la taille de nœud et le nombre de nœuds.

1. Dans la page **Ajouter un pool de nœuds**, sélectionnez **Ajouter**.
1. De retour sous l’onglet **Pools de nœuds** de la page **Créer un cluster Kubernetes**, sélectionnez **Suivant**.
1. Sous l’onglet **Réseau** de la page **Créer un cluster Kubernetes**, sélectionnez **Suivant**.
1. Sous l’onglet **Intégration** de la page **Créer un cluster Kubernetes**, dans la liste déroulante **Registre de conteneurs**, sélectionnez l’entrée représentant le registre de conteneurs Azure créé dans l’exercice précédent, décochez la case **Activer les règles d’alerte recommandées**, assurez-vous de la désactivation de l’option **Azure Policy** et sélectionnez **Suivant**.
1. Sous l’onglet **Surveillance** de la page **Créer un cluster Kubernetes**, décochez la case **Activer les métriques Prometheus** et sélectionnez **Vérifier + créer**.
1. Sous l’onglet **Vérifier + créer** de la page **Créer un cluster Kubernetes**, sélectionnez **Créer**.

   > **Remarque :** Passez à l’exercice suivant sans attendre la fin du provisionnement du cluster AKS. Le processus de provisionnement peut prendre environ 5 minutes.

## Exercice 2 : Créer des images conteneur Linux et Windows et les stocker dans ACR
Dans cet exercice, vous créez des images Docker basées sur Linux et Windows, et vous les poussez dans le registre de conteneurs Azure que vous avez créé précédemment dans cet exercice.

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

## Exercice 3 : Déployer des images conteneur sur AKS 
Dans cet exercice, vous allez déployer deux images conteneurs que vous avez créées plus tôt dans cet exercice sur le cluster AKS.

> **Note:** Avant de poursuivre cet exercice, assurez-vous que l’approvisionnement du cluster AKS a réussi.

### Tâche 1 : Créer des espaces de noms AKS personnalisés
Dans cette tâche, vous allez créer deux espaces de noms sur le cluster AKS que vous avez créé précédemment dans cet exercice.

1. Dans le portail Azure, dans la zone de texte **Rechercher**, recherchez et sélectionnez **Services Kubernetes**.
1. Dans la page **Services Kubernetes**, sélectionnez **aks-01**.
1. Dans la page **aks-01**, dans le menu vertical, sélectionnez **Espaces de noms**.
1. Dans la page **Espaces de noms \| a-01**, sélectionnez **+ Créer** et, dans le menu déroulant, sélectionnez **Espace de noms**.
1. Dans le volet **Créer un espace de noms**, dans la zone de texte **Nom**, entrez **dev-node** et sélectionnez **Créer**.
1. Dans la page **Espaces de noms \| a-01**, sélectionnez **+ Créer** et, dans le menu déroulant, sélectionnez **Espace de noms**.
1. Dans le volet **Créer un espace de noms**, dans la zone de texte **Nom**, entrez **dev-dotnet** et sélectionnez **Créer**.

### Tâche 2 : créer un manifeste Kubernetes pour déployer l'image Linux
Dans cette tâche, vous allez créer un manifeste Kubernetes pour déployer l'image Linux sur le pool de nœuds Linux

1. Dans le portail Azure, sélectionnez l’icône **Cloud Shell**.
1. Vérifiez que **Bash** s’affiche dans le menu déroulant en haut à gauche du volet Cloud Shell.
1. Dans la session Bash de Azure Cloud Shell, créez un répertoire pour héberger le manifeste de déploiement pour le provisionnement des pods basés sur l’image Linux et accédez-y à partir du répertoire actuel en exécutant les commandes suivantes :

   ```bash
   mkdir ~/aks-l01
   cd ~/aks-l01
   ```

1. Dans la session Bash d’Azure Cloud Shell, utilisez l’éditeur intégré pour créer un fichier nommé aks-deployment-l01.yaml dans le répertoire aks-l01 et copiez-y le contenu suivant :

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: hellofromnode-deployment
     labels:
       environment: dev
       app: hellofromnode
   spec:
     replicas: 1
     template:
       metadata:
         name: hellofromnode
         labels:
           app: hellofromnode
       spec:
         nodeSelector:
           "kubernetes.io/os": linux
         containers:
         - name: hellofromnode
           image: ACR_NAME.azurecr.io/hellofromnode:v1.0
           resources:
             limits:
               cpu: 1
               memory: 800M
           ports:
             - containerPort: 80
     selector:
       matchLabels:
         app: hellofromnode
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: hellofromnode-service
   spec:
     type: LoadBalancer
     ports:
     - protocol: TCP
       port: 80
     selector:
       app: hellofromnode
   ```

   > **Note :** le déploiement crée des pods basés sur l’image conteneur Linux dans le pool de nœuds Linux dans le cluster AKS. En outre, le manifeste inclut un service qui fournira un accès équilibré aux pods dans le déploiement via une adresse IP publique sur le port 80.

1. Enregistrez les changements du fichier et fermez-le pour revenir à l’invite Bash.
1. À partir de la session Bash d’Azure Cloud Shell, remplacez l’espace réservé **ACR_NAME** dans le fichier aks-deployment-l01.yaml en exécutant les commandes suivantes :

   ```azurecli
   ACR_RGNAME='acr-01-RG'
   ACR_NAME=$(az acr list --resource-group $ACR_RGNAME --query "[].name" --output tsv)
   sed -i "s/ACR_NAME/$ACR_NAME/" ./aks-deployment-l01.yaml
   ```

### Tâche 3 : créer un manifeste Kubernetes pour déployer l'image Windows
Dans cette tâche, vous allez créer un manifeste Kubernetes pour déployer l'image Windows sur le pool de nœuds Windows

1. Dans la session Bash de Azure Cloud Shell, créez un répertoire pour héberger le manifeste de déploiement pour le provisionnement des pods basés sur l’image Windows et accédez-y à partir du répertoire actuel en exécutant les commandes suivantes :

   ```bash
   mkdir ~/aks-w01
   cd ~/aks-w01
   ```

1. Dans la session Bash d’Azure Cloud Shell, utilisez l’éditeur intégré pour créer un fichier nommé aks-deployment-w01.yaml dans le répertoire aks-w01 et copiez-y le contenu suivant :

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: hellofromdotnet-deployment
     labels:
       environment: dev
       app: hellofromdotnet
   spec:
     replicas: 1
     template:
       metadata:
         name: hellofromdotnet
         labels:
           app: hellofromdotnet
       spec:
         nodeSelector:
           "kubernetes.io/os": windows
         containers:
         - name: hellofromdotnet
           image: ACR_NAME.azurecr.io/hellofromdotnet:v1.0
           resources:
             limits:
               cpu: 1
               memory: 800M
           ports:
             - containerPort: 80
     selector:
       matchLabels:
         app: hellofromdotnet
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: hellofromdotnet-service
   spec:
     type: LoadBalancer
     ports:
     - protocol: TCP
       port: 80
     selector:
       app: hellofromdotnet
   ```

   > **Note :** le déploiement crée des pods basés sur l’image conteneur Windows dans le pool de nœuds Windows dans le cluster AKS. En outre, le manifeste inclut un service qui fournira un accès équilibré aux pods dans le déploiement via une adresse IP publique sur le port 80.

1. Enregistrez les changements du fichier et fermez-le pour revenir à l’invite Bash.
1. À partir de la session Bash d’Azure Cloud Shell, remplacez l’espace réservé **ACR_NAME** dans le fichier aks-deployment-l01.yaml en exécutant la commande suivante :

   ```azurecli
   sed -i "s/ACR_NAME/$ACR_NAME/" ./aks-deployment-w01.yaml
   ```

### Tâche 4 : effectuer des déploiements AKS en utilisant des fichiers manifestes YAML
Dans cette tâche, vous allez déployer les deux images conteneurs dans leurs espaces de noms et pools de nœuds respectifs dans le cluster AKS cible.

1. À partir de la session Bash d'Azure Cloud Shell, connectez-vous au cluster AKS en exécutant les commandes suivantes :

   ```azurecli
   AKSRG='aks-01-RG'
   AKSNAME='aks-01'
   az aks get-credentials --resource-group $AKSRG --name $AKSNAME
   ```

1. À partir de la session Bash d'Azure Cloud Shell, vérifiez que la connexion a réussi en exécutant la commande suivante :

   ```kubectl
   kubectl get nodes
   ```

   > **Note :** la sortie de la commande doit inclure la liste de tous les nœuds AKS (quatre dans notre cas).

1. À partir de la session Bash d’Azure Cloud Shell, créez le premier déploiement défini dans le fichier manifeste YAML correspondant dans l’espace de noms **dev-node** en exécutant les commandes suivantes :

   ```kubectl
   cd ~/aks-l01
   kubectl apply -f aks-deployment-l01.yaml -n=dev-node
   ```

   > **Note :** passez à l’étape suivante sans attendre que le déploiement se termine. Le provisionnement de toutes les ressources peut prendre quelques minutes.

1. À partir de la session Bash d’Azure Cloud Shell, créez le deuxième déploiement défini dans le fichier manifeste YAML correspondant en exécutant la commande suivante :

   ```kubectl
   cd ~/aks-w01
   kubectl apply -f aks-deployment-w01.yaml -n=dev-dotnet
   ```

   > **Note :** passez à l’étape suivante sans attendre que le déploiement se termine. Le provisionnement de toutes les ressources peut prendre quelques minutes.

# Exercice 4 : Évaluer le déploiement et déprovisionner toutes les ressources
Dans cet exercice, vous allez examiner les résultats des déploiements et déprovisionner toutes les ressources.

### Tâche 1 : Évaluer les déploiements et les services AKS
Dans cette tâche, vous allez examiner les résultats des deux déploiements, y compris les objets de déploiements et de services.

1. À partir de la session Bash d’Azure Cloud Shell, affichez l’état des deux déploiements en exécutant les commandes suivantes :

   ```kubectl
   kubectl get deployments -n=dev-node
   kubectl get deployments -n=dev-dotnet
   ```

   > **Remarque :** Avant de passer à l’étape suivante, vérifiez que les deux déploiements sont répertoriés avec l’état prêt. Si ce n’est pas le cas, attendez encore une minute, exécutez à nouveau les deux commandes susmentionnées et vérifiez à nouveau l’état des déploiements.

1. À partir de la session Bash d’Azure Cloud Shell, affichez l’état des deux services inclus dans les fichiers manifestes en exécutant les commandes suivantes :

   ```kubectl
   kubectl get services -n=dev-node
   kubectl get services -n=dev-dotnet
   ```

1. Assurez-vous que la liste de chaque service contient une valeur dans la colonne **EXTERNAL-IP**. 
1. Utilisez un navigateur web pour accéder aux adresses IP que vous avez identifiées à l’étape précédente, puis assurez-vous que les pages web résultantes affichent respectivement les messages **Hello World from Node** et **Hello World from .Net 7**.

### Tâche 2 : Supprimer toutes les ressources
Dans cette tâche, vous allez supprimer toutes les ressources provisionnées dans cet exercice.

1. À partir de la session Bash d’Azure Cloud Shell, affichez la liste des ressources dans les deux groupes de ressources provisionnés dans cet exercice en exécutant les commandes suivantes :

   ```azurecli
   az resource list --resource-group 'acr-01-RG' --query "[].name" --output tsv
   az resource list --resource-group 'aks-01-RG' --query "[].name" --output tsv
   ```

   > **Remarque :** Assurez-vous qu’il s’agit des ressources que vous souhaitez supprimer. Si c’est le cas, passez à l’étape suivante.

1. Depuis la session Bash d’Azure Cloud Shell, supprimez toutes les ressources provisionnées dans cet exercice en exécutant les commandes suivantes :

   ```azurecli
   az group delete --name 'acr-01-RG' --no-wait --yes
   az group delete --name 'aks-01-RG' --no-wait --yes
   ```

   > **Note :** la commande s’exécute de manière asynchrone (telle qu’elle est appliquée par le paramètre --nowait). Ainsi, même les deux commandes retournent immédiatement à l’invite de l’interpréteur de commandes Bash, quelques minutes sont nécessaires avant que les groupes de ressources et leurs ressources soient réellement supprimés.

1. Fermez le volet Azure Cloud Shell.
