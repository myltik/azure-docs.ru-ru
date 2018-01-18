---
title: "Аутентификация с помощью реестра контейнеров Azure из Службы контейнеров Azure"
description: "Узнайте, как обеспечить доступ к образам в закрытом реестре контейнеров из Службы контейнеров Azure с помощью субъекта-службы Azure Active Directory."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/12/2018
ms.author: nepeters
ms.openlocfilehash: b9742e3994cf4c02ce3fef87099303921907bf77
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/13/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-service"></a>Аутентификация с помощью реестра контейнеров Azure из Службы контейнеров Azure

При использовании реестра контейнеров Azure (ACR) со Службой контейнеров Azure (AKS) необходимо установить механизм аутентификации. В этом документе описаны рекомендуемые конфигурации для аутентификации между этими двумя службами Azure.

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
az role assignment create --assignee $CLIENT_ID --role Read --scope $ACR_ID
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
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Read --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Учетные данные субъекта-службы теперь можно хранить в [секрете извлечения образа][image-pull-secret] Kubernetes и ссылаться на них при запуске контейнеров в кластере AKS. 

Следующая команда создает секрет Kubernetes. Замените имя сервера сервером входа в систему ACR, имя пользователя идентификатором субъекта-службы и пароль паролем субъекта-службы.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> 
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
