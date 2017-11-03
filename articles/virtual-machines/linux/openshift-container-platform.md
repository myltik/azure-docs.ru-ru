---
title: "Развертывание платформы контейнеров OpenShift в Azure | Документация Майкрософт"
description: "Развертывание платформы контейнеров OpenShift в Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: c91b7232b2f87e0b4b5e659126b96a6ef8b4202c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Развертывание платформы контейнеров OpenShift в Azure

Существует несколько способов развертывания платформы контейнеров OpenShift в Azure. Вы можете вручную развернуть все необходимые компоненты инфраструктуры Azure, а затем следовать инструкциям из [документации](https://docs.openshift.com/container-platform/3.6/welcome/index.html) по платформе контейнеров OpenShift.
Вы также можете использовать имеющийся шаблон Resource Manager, упрощающий развертывание кластера платформы контейнеров OpenShift. Однократно такой шаблон можно получить [здесь](https://github.com/Microsoft/openshift-container-platform/).

Другой вариант — использовать [предложение Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Для обоих вариантов подписка Red Hat является обязательной. Во время развертывания экземпляр RHEL регистрируется в подписке Red Hat и прикрепляется к идентификатору пула, который содержит права доступа к платформе контейнеров OpenShift.
Убедитесь, что у вас есть действительное имя пользователя, пароль и идентификатор пула подписки диспетчера Red Hat (имя пользователя RHSM, пароль RHSM и идентификатор пула). Данные можно проверить, выполнив вход на сайте https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Развертывание платформы контейнеров OpenShift с помощью шаблона Resource Manager

Для развертывания с использованием шаблона Resource Manager используется файл параметров, в котором указываются все входные параметры. Если вы хотите настроить какие-либо элементы развертывания, на которые не распространяются входные параметры, скопируйте репозиторий GitHub и измените соответствующие элементы.

Некоторые общие параметры настройки включают в себя (но не ограничиваясь ими):

- CIDR виртуальной сети [переменная в azuredeploy.json];
- размер виртуальной машины-бастиона [переменная в azuredeploy.json];
- соглашения об именовании [переменные в azuredeploy.json];
- особенности кластера OpenShift, изменяемые в файле hosts [deployOpenShift.sh].

### <a name="configure-parameters-file"></a>Настройка файла параметров

Используйте значение `appId` созданной ранее субъект-службы для параметра `aadClientId`. 

В следующем примере создается файл параметров с именем **azuredeploy.parameters.json** со всеми необходимыми входными данными.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Замените элементы в фигурных скобках {} требуемыми параметрами.

### <a name="deploy-using-azure-cli"></a>Развертывание с помощью Azure CLI

> [!NOTE] 
> Для выполнения следующей команды необходим интерфейс командной строки версии не ниже 2.0.8. Узнать свою версию CLI вы можете с помощью команды `az --version`. Чтобы обновить версию CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

В следующем примере развертывается кластер OpenShift и все связанные ресурсы в группу ресурсов myResourceGroup с именем развертывания myOpenShiftCluster. Репозиторий Github ссылается непосредственно на шаблон и используется локальный файл параметров с именем **azuredeploy.parameters.json**.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Для завершения развертывания требуется не менее 30 минут в зависимости от общего количества развертываемых узлов. После завершения развертывания в терминале появятся сведения об URL-адресе консоли OpenShift, а также DNS-имя основного кластера OpenShift.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-using-openshift-container-platform-marketplace-offer"></a>Развертывание платформы контейнеров OpenShift с помощью предложения в Marketplace

Для развертывания платформы контейнеров OpenShift в Azure проще всего использовать [предложение Azure в Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Это самый простой вариант, но он имеет ограниченные возможности для настройки. Предложение включает в себя три варианта настройки:

- Небольшой. Развертывается невысокодоступный кластер с одним главным узлом, одним узлом инфраструктуры, двумя узлами приложений и одним узлом-бастионом. Все узлы имеют виртуальные машины стандартного размера DS2v2. Для этого кластера требуется 10 ядер, и он идеально подходит для тестирования с невысоким уровнем масштабируемости.
- Средний. Развертывается высокодоступный кластер с тремя главными узлами, двумя узлами инфраструктуры, четырьмя узлами приложений и одним узлом-бастионом. Все узлы, за исключением узла-бастиона, развертываются на стандартных виртуальных машинах размера DS3v2. Узел-бастион развертывается на виртуальной машине DS2v2. Этот кластер содержит 38 ядер.
- Большой. Развертывается высокодоступный кластер с тремя главными узлами, двумя узлами инфраструктуры, шестью узлами приложений и одним узлом-бастионом. Главный узел и узел инфраструктуры развертываются на стандартных виртуальных машинах размера DS3v2, узлы приложений развертываются на стандартных виртуальных машинах размера DS4v2, а узел-бастион является стандартной виртуальной машиной размера DS2v2. Этот кластер должен содержать 70 ядер.

Конфигурация поставщика облачных служб Azure является необязательной для кластеров среднего и большого размера. Кластеры малого размера не предоставляют возможности настройки поставщика облачных служб Azure.

## <a name="connect-to-the-openshift-cluster"></a>Подключение к кластеру OpenShift

После завершения развертывания подключитесь к консоли OpenShift через браузер с помощью `OpenShift Console Uri`. Кроме того, вы можете подключиться к основному кластеру OpenShift, используя команду ниже:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, кластер OpenShift и все связанные с ним ресурсы, выполнив команду [az group delete](/cli/azure/group#delete).

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок развертывания OpenShift в Azure](./openshift-troubleshooting.md)
- [Overview](https://docs.openshift.com/container-platform/3.6/getting_started/index.html) (Обзор)
