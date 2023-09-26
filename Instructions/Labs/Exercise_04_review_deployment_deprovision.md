---
Exercise:
  title: 'Exercice : Évaluer le déploiement et le déprovisionnement de toutes les ressources'
  module: Guided Project - Deploy applications to Azure Kubernetes Service
---
# Exercice 4 : Déployer des applications sur Azure Kubernetes Service (AKS)

## Objectifs

Ce projet guidé comprend les exercices suivants :

+ Exercice 1 : Provisionner Azure Container Registry (ACR) et Azure Kubernetes Service (AKS)
+ Exercice 2 : Créer des images conteneur Linux et Windows, puis les stocker dans ACR
+ Exercice 3 : Déployer des images conteneur sur AKS
+ **Exercice 4 : Évaluer le déploiement et déprovisionner toutes les ressources**

Dans cet exercice, vous évaluez le déploiement des services dans les exercices précédents et déprovisionnez toutes les ressources.

# Exercice 4 : Évaluer le déploiement et déprovisionner toutes les ressources
Dans cet exercice, vous allez examiner les résultats des déploiements et déprovisionner toutes les ressources.

>**Remarque** : Pour faire cet exercice, vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free/).
> Pour chaque propriété non spécifiée, utilisez la valeur par défaut.

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
Dans cette tâche, vous allez supprimer toutes les ressources provisionnées dans ce labo.

1. À partir de la session Bash d’Azure Cloud Shell, affichez la liste des ressources, dans les deux groupes de ressources provisionnés dans cet exercice, en exécutant les commandes suivantes :

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
