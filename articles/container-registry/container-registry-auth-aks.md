---
title: Аутентификация с помощью реестра контейнеров Azure из Службы Azure Kubernetes
description: Узнайте, как обеспечить доступ к образам в закрытом реестре контейнеров из Службы Azure Kubernetes с помощью субъекта-службы Azure Active Directory.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.openlocfilehash: 0888afbb9087251e2c9219e2eb32fbf0d5600304
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33767892"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Аутентификация с помощью реестра контейнеров Azure из Службы Azure Kubernetes

При использовании реестра контейнеров Azure (ACR) со Службой Azure Kubernetes (AKS) необходимо установить механизм аутентификации. В этом документе описаны рекомендуемые конфигурации для аутентификации между этими двумя службами Azure.

## <a name="grant-aks-access-to-acr"></a>Предоставление AKS доступа к ACR

При создании кластера AKS также создается субъект-служба, который предназначен для управления работоспособностью кластера с помощью ресурсов Azure. Субъект-служба может также использоваться для аутентификации с помощью реестра ACR. Для этого необходимо создать назначение ролей, чтобы предоставить субъекту-службе доступ на чтение ресурса ACR.

Следующий пример можно использовать для выполнения этой операции.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Доступ с помощью секрета Kubernetes

В некоторых экземплярах субъект-служба, используемый AKS, не допускает ограничения области по реестру ACR. В таких случаях можно создать уникальный субъект-службу и ограничить его только реестром ACR.

Следующий сценарий можно использовать для создания субъекта-службы.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Учетные данные субъекта-службы теперь можно хранить в [секрете извлечения образа][image-pull-secret] Kubernetes и ссылаться на них при запуске контейнеров в кластере AKS.

Следующая команда создает секрет Kubernetes. Замените имя сервера сервером входа в систему ACR, имя пользователя идентификатором субъекта-службы и пароль паролем субъекта-службы.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Секрет Kubernetes может использоваться в развертывании pod с использованием параметра `ImagePullSecrets`.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
