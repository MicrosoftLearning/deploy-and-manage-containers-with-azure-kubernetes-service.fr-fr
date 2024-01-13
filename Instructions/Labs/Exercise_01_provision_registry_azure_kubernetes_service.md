---
Exercise:
  title: "Exercice\_: Provisionner Azure Container Registry (ACR) et Azure Kubernetes Service (AKS)"
  module: Guided Project - Deploy applications to Azure Kubernetes Service
---

# Exercice : Déployer des applications sur Azure Kubernetes Service (AKS)

## Objectifs

Ce projet guidé comprend les exercices suivants :

+ **Exercice 1 : Provisionner Azure Container Registry (ACR) et Azure Kubernetes Service (AKS).**
+ Exercice 2 : Créer des images conteneur Linux et Windows et stockage de ces images dans Azure Container Registry.
+ Exercise 3 : Déployer des images conteneurs vers Azure Kubernetes Service.
+ Exercice 4 : Vérifier le déploiement et déprovisionner toutes les ressources.

Dans cet exercice, vous provisionnez des ressources Azure Container Registry et Azure Kubernetes Service.
## Exercice 1 : Provisionner Azure Container Registry (ACR) et Azure Kubernetes Service (AKS)
Dans cet exercice, vous créez un registre de conteneurs Azure et un cluster AKS.

>**Remarque** : Pour faire cet exercice, vous avez besoin d’un [abonnement Azure](https://azure.microsoft.com/free/).
> Pour toute propriété non spécifiée, utilisez la valeur par défaut.

### Tâche 1 : Créer un registre de conteneurs Azure
Dans cette tâche, vous créez un registre de conteneurs Azure

1. À partir de votre ordinateur, ouvrez une fenêtre de navigateur web et accédez au Portail Azure à l’adresse https://portal.azure.com.
1. Quand vous y êtes invité, connectez-vous en utilisant un compte d’utilisateur avec le rôle Propriétaire dans l’abonnement Azure que vous utilisez dans ce labo. 
1. Connectez-vous au portail Azure. 
1. Dans le portail Azure, dans la zone de texte **Rechercher**, recherchez et sélectionnez **Registres de conteneurs**.
1. Dans la page **Registres de conteneurs**, sélectionnez **+ Créer** et spécifiez les paramètres suivants :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|Nom de l’abonnement Azure que vous utilisez dans ce labo|
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
    |Abonnement|Nom de l’abonnement Azure que vous avez sélectionné dans le premier exercice de ce labo|
    |Groupe de ressources|Nom d’un nouveau groupe de ressources **aks-01-RG**|
    |Nom du réseau virtuel|**vnet-01**|
    |Région|Même région Azure que vous avez sélectionnée dans le premier exercice de ce labo|

1. Sous l’onglet **Informations de base** de la page **Créer un réseau virtuel**, sélectionnez **Suivant**.
1. Sous l’onglet **Sécurité** de la page **Créer un réseau virtuel**, acceptez les paramètres par défaut et sélectionnez **Suivant**.
1. Sous l’onglet **Adresses IP** de la page **Créer un réseau virtuel**, vérifiez que l’espace d’adressage IP est défini sur **10.0.0.0/16**, supprimez le sous-réseau **par défaut**, sélectionnez **Vérifier + créer** et, sous l’onglet **Vérifier + créer**, sélectionnez **Créer**.

   > **Remarque :** La création d’un réseau virtuel devant durer seulement quelques secondes, vous devez pouvoir passer directement à l’étape suivante.

1. Dans le portail Azure, dans la zone de texte **Rechercher**, recherchez et sélectionnez **Services Kubernetes**.
1. Dans la page **Services Kubernetes**, sélectionnez **+ Créer**, dans la liste déroulante, sélectionnez **Créer un cluster Kubernetes**, puis, sous l’onglet **Informations de base** de la page **Créer un cluster Kubernetes**, spécifiez les paramètres suivants :

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
    |Taille du nœud|**Standard B4ms**|
    |Méthode de mise à l’échelle|**Manuel**|
    |Nombre de nœuds|**2**|

   > **Remarque :** Vous devez peut-être augmenter les quotas de processeur virtuel ou changer la référence SKU VM pour prendre en charge la taille de nœud et le nombre de nœuds. Pour plus d’informations sur la procédure d’augmentation des quotas de processeurs virtuels, consultez l’article Microsoft Learn [Augmenter les quotas de processeurs virtuels d’une famille VM](https://learn.microsoft.com/en-us/azure/quotas/per-vm-quota-requests).

1. Sous l’onglet **Informations de base** de la page **Créer un cluster Kubernetes**, sélectionnez **Suivant : Pools de nœuds >** .
1. Sous l’onglet **Pools de nœuds** de la page **Créer un cluster Kubernetes**, sélectionnez **Suivant : Accès >** .
1. Sous l’onglet **Accès** de la page **Créer un cluster Kubernetes**, sélectionnez **Suivant : Réseau >** .
1. Sous l’onglet **Réseau** de la page **Créer un cluster Kubernetes**, sélectionnez l’option **Azure CNI**, dans la liste déroulante **Réseau virtuel**, sélectionnez **vnet-01**, puis, sous la zone de texte **Sous-réseau du cluster**, sélectionnez **Configuration du sous-réseau managé**.
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
    |Sous-réseau de cluster|**aks-subnet (10.0.0.0/20)**|
    |Plage d’adresses de service Kubernetes|**172.16.0.0/22**|
    |Adresse IP du service DNS Kubernetes|**172.16.3.254**|
    |Préfixe du nom DNS|**aks-01-dns**|
    |Activer le cluster privé|Désactivé|
    |Définir des plages d’adresses IP autorisées|Désactivé|
    |Stratégie réseau|**Aucun**|

1. Sous l’onglet **Réseau** de la page **Créer un cluster Kubernetes**, sélectionnez l’onglet **Pools de nœuds**.

   > **Remarque :** Vous ajoutez un pool de nœuds Windows au cluster. Cela nécessite de remplacer la configuration réseau par défaut **Kubenet** par **Azure CNI**. La configuration réseau Kubenet ne prend pas en charge les pools de nœuds Windows.

1. Sous l’onglet **Pools de nœuds** de la page **Créer un cluster Kubernetes**, sélectionnez **+ Ajouter un pool de nœuds**.
1. Dans la page **Ajouter un pool de nœuds**, spécifiez les paramètres suivants :

    |Paramètre|Valeur|
    |---|---|
    |Nom du pool de nœuds|**w1pool**|
    |Mode|**Utilisateur**|
    |Type de système d’exploitation|**Windows**|
    |Zone de disponibilité|**Aucun**|
    |Activer les instances Azure Spot|Désactivé|
    |Taille du nœud|**Standard B4s_v2**|
    |Méthode de mise à l’échelle|**Manuel**|
    |Nombre de nœuds|**2**|
    |Pods maximum par nœud|**30**|
    |Activer l’IP publique par nœud|Désactivé|

   > **Remarque :** Vous devez peut-être augmenter les quotas de processeur virtuel ou changer la référence SKU VM pour prendre en charge la taille de nœud et le nombre de nœuds. Pour plus d’informations sur la procédure d’augmentation des quotas de processeurs virtuels, consultez l’article Microsoft Learn [Augmenter les quotas de processeurs virtuels d’une famille VM](https://learn.microsoft.com/en-us/azure/quotas/per-vm-quota-requests).

1. Dans la page **Ajouter un pool de nœuds**, sélectionnez **Ajouter**.
1. De retour sous l’onglet **Pools de nœuds** de la page **Créer un cluster Kubernetes**, sélectionnez l’onglet **Intégrations**.
1. Sous l’onglet **Intégration** de la page **Créer un cluster Kubernetes**, dans la liste déroulante **Registre de conteneurs**, sélectionnez l’entrée représentant le registre de conteneurs Azure que vous avez créé dans l’exercice précédent, décochez la case **Activer les règles d’alerte recommandées**, vérifiez que l’option **Azure Policy** est désactivée, puis sélectionnez **Vérifier + créer**.
1. Sous l’onglet **Vérifier + créer** de la page **Créer un cluster Kubernetes**, sélectionnez **Créer**.

   > **Remarque :** Passez à l’exercice suivant sans attendre la fin du provisionnement du cluster AKS. Le processus de provisionnement peut prendre environ 5 minutes.
