---
Exercise:
  title: "Exercice\_: Déployer des images conteneur sur Azure Kubernetes Service"
  module: Guided Project - Deploy applications to Azure Kubernetes Service
---
# Exercice 3 : Déployer des applications sur Azure Kubernetes Service (AKS)

## Objectifs

Ce projet guidé comprend les exercices suivants :

+ Exercice 1 : Provisionner Azure Container Registry (ACR) et Azure Kubernetes Service (AKS).
+ Exercice 2 : Créer des images conteneur Linux et Windows et les stocker dans Azure Container Registry.
+ **Exercice 3 : Déployer des images conteneur sur Azure Kubernetes Service.**
+ Exercice 4 : Vérifier le déploiement et déprovisionner toutes les ressources.

Dans cet exercice, vous déployez des images conteneur sur Azure Kubernetes Service.

## Exercice 3 : Déployer des images conteneur sur AKS 
Dans cet exercice, vous allez déployer deux images conteneurs que vous avez créées plus tôt dans cet exercice sur le cluster AKS.
>**Remarque** : Pour faire cet exercice, vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free/).
> Pour toute propriété non spécifiée, utilisez la valeur par défaut.
> **Note:** Avant de poursuivre cet exercice, assurez-vous que l’approvisionnement du cluster AKS a réussi.

### Tâche 1 : Créer des espaces de noms AKS personnalisés
Dans cette tâche, vous allez créer deux espaces de noms sur le cluster AKS que vous avez créé précédemment dans ce laboratoire.

1. Dans le Portail Microsoft Azure, dans la zone de texte **Rechercher**, recherchez et sélectionnez **Services Kubernetes**.
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

