---
title: Развертывание платформы контейнеров OpenShift в Azure | Документация Майкрософт
description: Развертывание платформы контейнеров OpenShift в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: f1ba6a3d3b9e576d513b55beac4e9365102433e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29125747"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Развертывание платформы контейнеров OpenShift в Azure

Для развертывания платформы OpenShift Container Platform в Azure можно использовать один из нескольких способов:

- Вы можете вручную развернуть необходимые компоненты инфраструктуры Azure, а затем следовать инструкциям из [документации](https://docs.openshift.com/container-platform/3.6/welcome/index.html) по платформе OpenShift Container Platform.
- Также можно использовать существующий [шаблон Resource Manager](https://github.com/Microsoft/openshift-container-platform/), упрощающий развертывание кластера платформы OpenShift Container Platform.
- Другой вариант — использовать [предложение Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Для всех вариантов подписка Red Hat является обязательной. Во время развертывания экземпляр Red Hat Enterprise Linux регистрируется в подписке Red Hat и связывается с идентификатором пула, который содержит права доступа к платформе OpenShift Container Platform.
Убедитесь, что у вас есть действительное имя пользователя, пароль и идентификатор пула диспетчера подписки Red Hat (RHSM). Данные можно проверить, войдя на сайт https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Развертывание платформы OpenShift Container Platform с помощью шаблона Resource Manager

Для развертывания с использованием шаблона Resource Manager используется файл параметров, в котором указываются входные параметры. Чтобы настроить элементы развертывания, на которые не распространяются входные параметры, создайте вилку репозитория GitHub и измените соответствующие элементы.

Некоторые общие параметры настройки включают в себя (но не ограничиваясь этим):

- CIDR виртуальной сети (переменная в azuredeploy.json);
- размер виртуальной машины-бастиона (переменная в azuredeploy.json);
- соглашения об именовании (переменные в azuredeploy.json);
- особенности кластера OpenShift, изменяемые в файле hosts (deployOpenShift.sh).

### <a name="configure-the-parameters-file"></a>Настройка файла параметров

Используйте значение `appId` созданной ранее субъект-службы для параметра `aadClientId`. 

В примере ниже создается файл параметров с именем azuredeploy.parameters.json, который содержит все необходимые входные данные.

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

Замените элементы, заключенные в скобки, своими значениями.

### <a name="deploy-by-using-azure-cli"></a>Развертывание с помощью Azure CLI

> [!NOTE] 
> Для выполнения следующей команды необходим интерфейс командной строки версии не ниже 2.0.8. Узнать свою версию CLI можно с помощью команды `az --version`. Чтобы обновить версию CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

В следующем примере кластер OpenShift и все связанные ресурсы развертываются в группу ресурсов myResourceGroup с именем развертывания myOpenShiftCluster. Репозиторий GitHub ссылается непосредственно на шаблон, при этом используется локальный файл параметров с именем azuredeploy.parameters.json.

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

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Развертывание платформы OpenShift Container Platform с помощью предложения Azure Marketplace

Для развертывания платформы OpenShift Container Platform в Azure проще всего использовать [предложение Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Это самый простой вариант, но он имеет ограниченные возможности для настройки. Предложение включает в себя три варианта настройки:

- **Малый**. Развертывается невысокодоступный кластер с одним главным узлом, одним узлом инфраструктуры, двумя узлами приложений и одним узлом-бастионом. Все узлы имеют виртуальные машины стандартного размера DS2v2. Для этого кластера требуется 10 ядер, и он идеально подходит для тестирования с невысоким уровнем масштабируемости.
- **Средний**. Развертывается высокодоступный кластер с тремя главными узлами, двумя узлами инфраструктуры, четырьмя узлами приложений и одним узлом-бастионом. Все узлы, за исключением узла-бастиона, развертываются на стандартных виртуальных машинах размера DS3v2. Узел-бастион развертывается на стандартной виртуальной машине DS2v2. Этот кластер содержит 38 ядер.
- **Большой**. Развертывается высокодоступный кластер с тремя главными узлами, двумя узлами инфраструктуры, шестью узлами приложений и одним узлом-бастионом. Главные узлы и узлы инфраструктуры развертываются на стандартных виртуальных машинах размера DS3v2. Узлы приложения развертываются на стандартных виртуальных машинах размера DS4v2, а узел бастиона — на стандартной виртуальной машине размера DS2v2. Этот кластер должен содержать 70 ядер.

Конфигурация поставщика облачных решений Azure является необязательной для кластеров среднего и большого размера. Кластеры малого размера не позволяют настраивать поставщика облачных решений Azure.

## <a name="connect-to-the-openshift-cluster"></a>Подключение к кластеру OpenShift

После завершения развертывания подключитесь к консоли OpenShift через браузер, используя `OpenShift Console Uri`. Кроме того, к главному узлу OpenShift можно подключиться с помощью следующей команды:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов, кластер OpenShift и все связанные ресурсы больше не нужны, их можно удалить с помощью команды [az group delete](/cli/azure/group#az_group_delete).

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок с развертыванием OpenShift в Azure](./openshift-troubleshooting.md)
- [Overview](https://docs.openshift.com/container-platform/3.6/getting_started/index.html) (Обзор)
