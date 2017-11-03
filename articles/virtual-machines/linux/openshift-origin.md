---
title: "Развертывание источника OpenShift в Azure | Документация Майкрософт"
description: "Разверните источник OpenShift в Azure."
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
ms.openlocfilehash: 1a40c4cc064b32aced7e976f40f6ed6a57e62204
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-origin-in-azure"></a>Развертывание источника OpenShift в Azure

Существует несколько способов развертывания источника OpenShift в Azure. Вы можете вручную развернуть все необходимые компоненты инфраструктуры Azure, а затем следовать инструкциям из [документации](https://docs.openshift.org/3.6/welcome/index.html) по источнику OpenShift.
Вы также можете использовать существующий шаблон Resource Manager, который упрощает развертывание кластера источника OpenShift. Однократно такой шаблон можно получить [здесь](https://github.com/Microsoft/openshift-origin).

## <a name="deploy-using-the-openshift-origin-template"></a>Развертывание с помощью шаблона источника OpenShift

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

### <a name="deploy-using-azure-cli"></a>Развертывание с помощью Azure CLI


> [!NOTE] 
> Для выполнения следующей команды необходим интерфейс командной строки версии не ниже 2.0.8. Узнать свою версию CLI вы можете с помощью команды `az --version`. Чтобы обновить версию CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

В следующем примере развертывается кластер OpenShift и все связанные ресурсы в группу ресурсов myResourceGroup с именем развертывания myOpenShiftCluster. Репозиторий Github ссылается непосредственно на шаблон и используется локальный файл параметров с именем **azuredeploy.parameters.json**.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Для завершения развертывания требуется не менее 25 минут в зависимости от общего количества развертываемых узлов. После завершения развертывания в терминале появятся сведения об URL-адресе консоли OpenShift, а также DNS-имя основного кластера OpenShift.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>Подключение к кластеру OpenShift

После завершения развертывания подключитесь к консоли OpenShift через браузер с помощью `OpenShift Console Uri`. Кроме того, вы можете подключиться к основному кластеру OpenShift, используя команду ниже:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, кластер OpenShift и все связанные с ним ресурсы, выполнив команду [az group delete](/cli/azure/group#delete).

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок развертывания OpenShift в Azure](./openshift-troubleshooting.md)
- [Overview](https://docs.openshift.org/latest/getting_started/index.html) (Обзор)
