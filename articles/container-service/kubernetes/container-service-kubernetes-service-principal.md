---
title: "Субъект-служба для кластера Azure Kubernetes | Документация Microsoft"
description: "Сведения о настройке субъекта-службы Azure Active Directory для кластера Kubernetes и управлении им в Службе контейнеров Azure."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 14975454cbc0afcfbdbd3aa6b52983be4d4b1785
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>Настройка субъекта-службы Azure AD для кластера Kubernetes в Службе контейнеров


[Субъект-служба Azure Active Directory](../../active-directory/develop/active-directory-application-objects.md) используется кластером Kubernetes в Службе контейнеров Azure для обеспечения взаимодействия с API-интерфейсами Azure. Субъект-служба используется для динамического управления ресурсами, например [определяемыми пользователем маршрутами](../../virtual-network/virtual-networks-udr-overview.md) и [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) уровня 4.


В этой статье показано, как настроить субъект-службу для кластера Kubernetes. Например, если вы установили и настроили [Azure CLI 2.0](/cli/azure/install-az-cli2), выполните команду [`az acs create`](/cli/azure/acs#create), чтобы одновременно создать кластер Kubernetes и субъект-службу.


## <a name="requirements-for-the-service-principal"></a>Требования для субъекта-службы

Вы можете использовать имеющийся субъект-службу Azure AD, который соответствует требованиям ниже, или создать другой.

* **Область.** Подписка, используемая для развертывания кластера.

* **Роль** — **Участник**.

* **Секрет клиента** — должен быть паролем. Сейчас субъект-службу нельзя использовать для проверки подлинности сертификата.

> [!IMPORTANT]
> Чтобы создать субъект-службу, вы должны иметь права на регистрацию приложения в клиенте Azure AD и назначение приложению роли в подписке Azure. Наличие этих разрешений можно [проверить на портале](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>Вариант 1. Создание субъекта-службы в Azure AD

Создать субъект-службу в Azure AD перед развертыванием кластера Kubernetes можно разными способами.

Следующие примеры команд показывают, как это сделать с помощью [Azure CLI 2.0](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md). Кроме того, субъект-службу можно создать с помощью [Azure PowerShell](../../azure-resource-manager/resource-group-authenticate-service-principal.md), [портала](../../azure-resource-manager/resource-group-create-service-principal-portal.md) или других методов.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

Выходные данные должны быть следующего содержания (здесь показана исправленная версия):

![Создание субъекта-службы](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Выделенные строки — это **идентификатор клиента** (`appId`) и **секрет клиента** (`password`), которые используются в качестве параметров субъекта-службы для развертывания кластера.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Указание субъекта-службы во время создания кластера Kubernetes

Создавая кластер Kubernetes, укажите в качестве параметров **идентификатор клиента** (или `appId` для идентификатора приложения) и **секрет клиента** (`password`) существующего субъекта-службы. Убедитесь, что субъект-служба соответствует требованиям, приведенным в начале этой статьи.

Развертывая кластер Kubernetes, вы можете указать эти параметры с помощью [Azure CLI 2.0](container-service-kubernetes-walkthrough.md), [портала Azure](../dcos-swarm/container-service-deployment.md) или других методов.

>[!TIP]
>В качестве **идентификатора клиента** обязательно используйте `appId`, а не `ObjectId` субъекта-службы.
>

В следующем примере показан один способ передачи параметров с помощью Azure CLI 2.0. В этом примере используется [шаблон быстрого запуска Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Скачайте](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) файл параметров шаблона `azuredeploy.parameters.json` из репозитория GitHub.

2. Чтобы указать субъект-службу, присвойте в файле значения параметрам `servicePrincipalClientId` и `servicePrincipalClientSecret`. (Также вам нужно присвоить собственные значения параметрам `dnsNamePrefix` и `sshRSAPublicKey`. Последний параметр — это открытый ключ SSH для доступа к кластеру.) Сохраните файл.

    ![Передача параметров субъекта-службы](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Выполните следующую команду, используя параметр `--parameters`, чтобы указать путь к файлу azuredeploy.parameters.json. Эта команда развертывает кластер в созданной группе ресурсов `myResourceGroup` в регионе "Западная часть США".

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>Вариант 2. Создание субъекта-службы при создании кластера с помощью `az acs create`

При создании кластера Kubernetes с использованием команды [`az acs create`](/cli/azure/acs#create) вы можете создать субъект-службу автоматически.

Как и в случае с другими вариантами создания кластера Kubernetes, выполняя команду `az acs create`, вы можете указать параметры для существующего субъекта-службы. Но даже если вы не настроите эти параметры, Azure CLI создаст субъект-службу автоматически, который будет использоваться в Службе контейнеров. Субъект-служба создается открытым образом во время развертывания.

Следующая команда создает кластер Kubernetes, ключи SSH и учетные данные субъекта-службы:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Если учетная запись на имеет разрешений по подписке и разрешений Azure AD на создание субъекта-службы, команда вернет сообщение об ошибке, аналогичное следующему: `Insufficient privileges to complete the operation.`.
>

## <a name="additional-considerations"></a>Дополнительные замечания

* Если у вас нет разрешений на создание субъекта-службы в подписке, обратитесь к администратору подписки или Azure AD для назначения необходимых разрешений или запросите субъект-службу для использования со Службой контейнеров Azure.

* Субъект-служба для Kubernetes входит в конфигурацию кластера. Тем не менее не используйте идентификатор для развертывания кластера.

* Все субъекты-службы связаны с определенными приложениями Azure AD. Субъект-служба для кластера Kubernetes может быть связан с любым допустимым именем приложения Azure AD, например `https://www.contoso.org/example`. URL-адрес приложения не обязательно должен быть реальной конечной точкой.

* Указывая **идентификатор клиента** субъекта-службы, вы можете использовать значение `appId` (как показано в этой статье) или соответствующее имя (`name`) субъекта-службы, например `https://www.contoso.org/example`.

* На главной виртуальной машине и виртуальной машине агента в кластере Kubernetes учетные данные субъекта-службы хранятся в файле /etc/kubernetes/azure.json.

* Если вы используете команду `az acs create`, чтобы автоматически создать субъект-службу, его учетные данные записываются в файл ~/.azure/acsServicePrincipal.json на компьютере, с которого выполняется команда.

* При автоматическом создании субъекта-службы с использованием команды `az acs create` субъект-служба также позволяет проверять подлинность с помощью [реестра контейнеров Azure](../../container-registry/container-registry-intro.md), созданного в той же подписке.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [начать работу с Kubernetes](container-service-kubernetes-walkthrough.md) в кластере службы контейнеров.

* Сведения об устранении неполадок с субъектом-службой для Kubernetes см. в [документации по модулю ACS](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting).
