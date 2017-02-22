---
title: "Субъект-служба для кластера Azure Kubernetes | Документация Microsoft"
description: "Создание субъекта-службы Azure Active Directory в кластере Kubernetes службы контейнеров Azure и управление им"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 5af9b5fdaf228edd54900855d0eac5d90ea3db38
ms.openlocfilehash: 0121896aa27677080d6b240fdafff3c7e19683d9


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>Сведения о субъекте-службе Azure Active Directory для кластера Kubernetes в службе контейнеров Azure



[Субъект-служба Azure Active Directory](../active-directory/active-directory-application-objects.md) используется кластером Kubernetes в службе контейнеров Azure в качестве учетной записи службы, обеспечивающей взаимодействие с API-интерфейсами Azure. Субъект-служба используется для динамического управления ресурсами, например определяемыми пользователем маршрутами и Azure Load Balancer уровня 4.

В этой статье показано, как можно указать субъект-службу для кластера Kubernetes. Например, если вы установили и настроили [Azure CLI 2.0 (предварительная версия)](https://docs.microsoft.com/cli/azure/install-az-cli2), выполните команду [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create), чтобы одновременно создать кластер Kubernetes и субъект-службу.

> [!NOTE]
> Поддержка Kubernetes в службе контейнеров Azure сейчас доступна в предварительной версии.


## <a name="requirements-for-the-service-principal"></a>Требования для субъекта-службы

Ниже приведены требования для субъекта-службы Azure Active Directory в кластере Kubernetes в службе контейнеров Azure. 

* **Область** — подписка Azure, в которой развернут кластер.

* **Роль** — **Участник**.

* **Секрет клиента** — должен быть паролем. Сейчас субъект-службу нельзя использовать для проверки подлинности сертификата.

> [!NOTE]
> Все субъекты-службы связаны с определенными приложениями Azure Active Directory. Субъект-служба для кластера Kubernetes может быть связан с любым допустимым именем приложения Azure Active Directory.
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Варианты использования субъекта-службы для кластера Kubernetes

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>Вариант 1. Передача идентификатора и секрета клиента субъекта-службы

Создавая кластер Kubernetes, укажите в качестве параметров **идентификатор клиента** (или `appId` для идентификатора приложения) и **секрет клиента** (`password`) существующего субъекта-службы. Если вы используете существующий субъект-службу, убедитесь, что он соответствует требованиям, указанным в предыдущем разделе. Если необходимо создать субъект-службу, см. раздел [Создание субъекта-службы в Azure Active Directory](#create-a-service-principal-in-azure-active-directory) далее в этой статье.

[Развертывая кластер Kubernetes](./container-service-deployment.md), вы можете указать эти параметры с помощью портала, интерфейса командной строки Azure (Azure CLI) 2.0 (предварительная версия), Azure PowerShell или других методов.

>[!TIP] 
>В качестве **идентификатора клиента** обязательно используйте `appId`, а не `ObjectId` субъекта-службы.
>

В следующем примере показан один способ передачи параметров с помощью предварительной версии Azure CLI 2.0 (см. [инструкции по установке и настройке](/cli/azure/install-az-cli2)). В этом примере используется [шаблон быстрого запуска Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

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


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20-preview"></a>Вариант 2. Создание субъекта-службы при создании кластера с помощью Azure CLI 2.0 (предварительная версия)

Если вы установили и настроили [Azure CLI 2.0 (предварительная версия)](https://docs.microsoft.com/cli/azure/install-az-cli2), выполните команду [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create), чтобы [создать кластер](./container-service-create-acs-cluster-cli.md).

Как и в случае с другими вариантами создания кластера Kubernetes, выполняя команду `az acs create`, вы можете указать параметры для существующего субъекта-службы. Но даже если вы не настроите эти параметры, служба контейнеров Azure создаст субъект-службу автоматически. Субъект-служба создается открытым образом во время развертывания. 

Следующая команда создает кластер Kubernetes, ключи SSH и учетные данные субъекта-службы:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Создание субъекта-службы в Azure Active Directory

Создать субъект-службу в Azure Active Directory для использования в кластере Kubernetes можно разными способами. 

Следующие примеры команд показывают, как это сделать с помощью [Azure CLI 2.0 (предварительная версия)](https://docs.microsoft.com/cli/azure/install-az-cli2). Кроме того, субъект-службу можно создать с помощью [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md), [классического портала](../azure-resource-manager/resource-group-create-service-principal-portal.md) или других методов.

> [!IMPORTANT]
> Обязательно ознакомьтесь с требованиями к субъекту-службе, приведенными ранее в этой статье.
>

```azurecli
az login

az account set --subscription "mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

Это команда возвращает результат следующего содержания (здесь показана исправленная версия):

![Создание субъекта-службы](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Выделенные строки — это **идентификатор клиента** (`appId`) и **секрет клиента** (`password`), которые используются в качестве параметров субъекта-службы для развертывания кластера.


Чтобы подтвердить субъект-службу, откройте новую оболочку и выполните следующую команду, заменив значения параметров `appId`, `password` и `tenant` собственными:

```azurecli 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>Дополнительные замечания


* Указывая **идентификатор клиента** субъекта-службы, вы можете использовать значение `appId` (как показано в этой статье) или соответствующее имя (`name`) субъекта-службы (например, `https://www.contoso.org/example`).

* Если вы используете команду `az acs create`, чтобы автоматически создать субъект-службу, его учетные данные записываются в файл ~/.azure/acsServicePrincipal.json на компьютере, с которого выполняется команда.

* На главной виртуальной машине и виртуальной машине узла в кластере Kubernetes учетные данные субъекта-службы хранятся в файле /etc/kubernetes/azure.json.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [начать работу с Kubernetes](container-service-kubernetes-walkthrough.md) в кластере службы контейнеров.



<!--HONumber=Feb17_HO1-->


