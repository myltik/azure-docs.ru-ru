---
title: "Субъект-служба для кластера Azure Kubernetes | Документация Microsoft"
description: "Сведения о настройке субъекта-службы Azure Active Directory для кластера Kubernetes и управлении им в AKS."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6c61d99f1d023ac643455faae10ef284f1f5bb14
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>Субъекты-службы со Службой контейнеров Azure (AKS)

Для взаимодействия с API-интерфейсами Azure кластеру AKS требуется [субъект-служба Azure Active Directory](../active-directory/develop/active-directory-application-objects.md). Субъект-служба используется для динамического управления ресурсами, например [определяемыми пользователем маршрутами](../virtual-network/virtual-networks-udr-overview.md) и [Azure Load Balancer](../load-balancer/load-balancer-overview.md) уровня 4.

В этой статье показано, как настроить субъект-службу для кластера Kubernetes в AKS.

## <a name="before-you-begin"></a>Перед началом работы

В действиях, описанных в этом документе, предполагается, что кластер AKS создан и с ним установлено соединение kubectl. Если вам требуются эти компоненты, см. статью [Deploy an Azure Container Service (AKS) cluster](./kubernetes-walkthrough.md) (Развертывание кластера Службы контейнера Azure (AKS)).

Чтобы создать субъект-службу в Azure AD, вы должны иметь права на регистрацию приложения в клиенте Azure AD и назначение приложению роли в подписке Azure. Если у вас нет необходимых разрешений, вам может потребоваться попросить администратора Azure AD или администратора подписки предоставить их или предварительно создать субъект-службу для кластера Kubernetes.

Кроме того, нужно установить и настроить Azure CLI версии 2.0.21 или выше. Чтобы узнать версию, выполните команду az --version. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-sp-with-aks-cluster"></a>Создание субъекта-службы с кластером AKS

При развертывании кластера AKS с помощью команды `az aks create` вы можете автоматически создать субъект-службу.

В следующем примере создается кластер AKS, и так как не указан имеющийся субъект-служба, для кластера создается новый. Чтобы завершить эту операцию, учетной записи требуются необходимые права для создания субъекта-службы.

```azurecli
az aks create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Использование имеющегося субъекта-службы

Вы можете использовать с кластером AKS имеющийся субъект-службу Azure AD или заранее создать для него новый. Это полезно при развертывании кластера с портала Azure, где необходимо указать сведения о субъекте-службе.

При использовании имеющегося субъекта-службы он должен соответствовать следующим требованиям:

- Область — подписка, используемая для развертывания кластера.
- Роль — "Участник".
- Секрет клиента — должен быть паролем.

## <a name="pre-create-a-new-sp"></a>Предварительное создание субъекта-службы

Чтобы создать субъект-службу с помощью Azure CLI, используйте команду [az ad sp create-for-rbac]().

```azurecli
id=$(az account show --query id --output tsv)
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$id"
```

Результат аналогичен приведенному ниже. Запишите значения `appId` и `password`. Эти значения нужны для создания кластера AKS.

```
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Использование имеющегося субъекта-службы

При использовании предварительно созданного субъекта-службы укажите `appId` и `password` как значения аргументов в команде `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-princal <appId> ----client-secret <password>
```

Если кластер AKS развертывается на портале Azure, введите их в форме конфигурации кластера AKS.

![Изображение перехода к приложению Azure для голосования](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Дополнительные замечания

При работе с субъектами-службами AKS и Azure AD имейте в виду следующее.

* Субъект-служба для Kubernetes входит в конфигурацию кластера. Тем не менее не используйте идентификатор для развертывания кластера.
* Все субъекты-службы связаны с определенными приложениями Azure AD. Субъект-служба для кластера Kubernetes может быть связан с любым допустимым именем приложения Azure AD, например `https://www.contoso.org/example`. URL-адрес приложения не обязательно должен быть реальной конечной точкой.
* Указывая **идентификатор клиента** субъекта-службы, вы можете использовать значение `appId` (как показано в этой статье) или соответствующее имя (`name`) субъекта-службы, например `https://www.contoso.org/example`.
* На главной виртуальной машине и виртуальной машине узла в кластере Kubernetes учетные данные субъекта-службы хранятся в файле /etc/kubernetes/azure.json.
* Если вы используете команду `az aks create`, чтобы автоматически создать субъект-службу, его учетные данные записываются в файл ~/.azure/acsServicePrincipal.json на компьютере, с которого выполняется команда.
* При автоматическом создании субъекта-службы с использованием команды `az aks create` субъект-служба также позволяет проверять подлинность с помощью [реестра контейнеров Azure](../container-registry/container-registry-intro.md), созданного в той же подписке.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о субъектах-службах Azure Active Directory см. в документации по приложениям Azure AD.

> [!div class="nextstepaction"]
> [Объекты приложения и субъекта-службы в Azure Active Directory (Azure AD)](../active-directory/develop/active-directory-application-objects.md)
