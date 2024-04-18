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
   |Emplacement|Région Azure dans laquelle vous pouvez créer un registre de conteneurs Azure et un cluster AKS|
   |Utiliser des zones de disponibilité|Désactivé|
   |Plan tarifaire|**De base**|

1. Dans la page **Registres de conteneurs**, sélectionnez **Vérifier + créer** et, sous l’onglet **Vérifier + créer**, sélectionnez **Créer**.

   > **Remarque :** Passez à l’exercice suivant sans attendre la fin du provisionnement d’Azure Container Registry.

### Tâche 2 : Créer un réseau virtuel Azure et un cluster AKS
Dans cette tâche, vous allez créer un réseau virtuel Azure et y déployer un cluster AKS comprenant un pool de nœuds Windows.

> **Remarque :** Alors que vous pouvez créer un réseau virtuel lors du provisionnement d’un cluster AKS, le déploiement de clusters AKS dans un réseau virtuel existant demande de prendre en considération d’autres points que vous devez connaître.

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
   - Dans la page **Sélectionner une taille de machine virtuelle**, dans la liste des tailles de machine virtuelle, sélectionnez **D2s_v3**, puis cliquez sur **Sélectionner**.
   - De retour dans la page **Mettre à jour le pool de nœuds**, définissez **Méthode de mise à l’échelle** sur **Manuelle** et **Nombre de nœuds** sur **2**.
   - Dans la page **Mettre à jour le pool de nœuds**, sélectionnez **Mettre à jour**.

   > **Remarque :** Vous devez peut-être augmenter les quotas de processeur virtuel ou changer la référence SKU VM pour prendre en charge la taille de nœud et le nombre de nœuds. Pour plus d’informations sur la procédure d’augmentation des quotas de processeurs virtuels, consultez l’article Microsoft Learn [Augmenter les quotas de processeurs virtuels d’une famille VM](https://learn.microsoft.com/en-us/azure/quotas/per-vm-quota-requests).

   > **Remarque :** Vous ajoutez un pool de nœuds Windows au cluster. Cela nécessite de remplacer la configuration réseau par défaut **Kubenet** par **Azure CNI**. La configuration réseau Kubenet ne prend pas en charge les pools de nœuds Windows.

1. De retour sous l’onglet **Pools de nœuds** de la page **Créer un cluster Kubernetes**, sélectionnez **Suivant**.
1. Sous l’onglet **Mise en réseau** de la page **Créer un cluster Kubernetes**, vérifiez que l’option **Azure CNI** est sélectionnée, cochez la case **Apporter votre propre réseau virtuel** puis, dans la liste déroulante **Réseau virtuel**, sélectionnez **vnet-01** et, sous la zone de texte **Sous-réseau du cluster**, sélectionnez **Gérer la configuration du sous-réseau**.
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
   |Taille du nœud|**D2s_v3**|
   |Méthode de mise à l’échelle|**Manuel**|
   |Nombre de nœuds|**2**|
   |Pods maximum par nœud|**30**|
   |Activer l’IP publique par nœud|Désactivé|

   > **Remarque :** Ici aussi, vous devrez probablement augmenter les quotas de processeur virtuel ou changer la référence SKU de machine virtuelle pour prendre en charge la taille de nœud et le nombre de nœuds.

1. Dans la page **Ajouter un pool de nœuds**, sélectionnez **Ajouter**.
1. De retour sous l’onglet **Pools de nœuds** de la page **Créer un cluster Kubernetes**, sélectionnez **Suivant**.
1. Sous l’onglet **Réseau** de la page **Créer un cluster Kubernetes**, sélectionnez **Suivant**.
1. Sous l’onglet **Intégration** de la page **Créer un cluster Kubernetes**, dans la liste déroulante **Registre de conteneurs**, sélectionnez l’entrée représentant le registre de conteneurs Azure créé dans l’exercice précédent, vérifiez que l’option **Azure Policy** est désactivée, puis sélectionnez **Suivant**.
1. Sous l’onglet **Surveillance** de la page **Créer un cluster Kubernetes**, décochez la case **Activer les métriques Prometheus**, décochez la case **Activer les règles d’alerte recommandées**, puis sélectionnez **Vérifier + créer**.
1. Sous l’onglet **Vérifier + créer** de la page **Créer un cluster Kubernetes**, sélectionnez **Créer**.

   > **Remarque :** Passez à l’exercice suivant sans attendre la fin du provisionnement du cluster AKS. Le processus de provisionnement peut prendre environ 5 minutes.
