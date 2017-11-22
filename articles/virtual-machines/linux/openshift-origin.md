---
title: "Развертывание OpenShift Origin в Azure | Документация Майкрософт"
description: "Развертывание OpenShift Origin в Azure."
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
ms.openlocfilehash: 1860ede19202566947b68b715e6bd354f64c1085
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2017
---
# <a name="deploy-openshift-origin-in-azure"></a>Развертывание OpenShift Origin в Azure

Существует два способа развертывания OpenShift Origin в Azure.

- Можно вручную развернуть все необходимые компоненты инфраструктуры Azure, а затем выполнить инструкции из [документации OpenShift Origin](https://docs.openshift.org/3.6/welcome/index.html).
- Можно воспользоваться [шаблоном Resource Manager](https://github.com/Microsoft/openshift-origin), который упрощает развертывание кластера OpenShift Origin.

## <a name="deploy-by-using-the-openshift-origin-template"></a>Развертывание с помощью шаблона OpenShift Origin

Используйте для параметра `aadClientId` значение `appId` из созданного ранее субъекта-службы.

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

### <a name="deploy-by-using-azure-cli"></a>Развертывание с помощью Azure CLI


> [!NOTE] 
> Для выполнения следующей команды необходим интерфейс командной строки версии не ниже 2.0.8. Узнать свою версию CLI можно с помощью команды `az --version`. Чтобы обновить версию CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

В примере ниже кластер OpenShift и все связанные ресурсы развертываются в группу ресурсов myResourceGroup с именем развертывания myOpenShiftCluster. Шаблон хранится в репозитории GitHub, и ссылка на него указывается в локальном файле параметров с именем azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Развертывание длится не менее 25 минут в зависимости от общего количества развертываемых узлов. После завершения развертывания в терминале отобразятся URL-адрес консоли OpenShift и DNS-имя главного узла OpenShift.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>Подключение к кластеру OpenShift

После завершения развертывания подключитесь к консоли OpenShift через браузер, используя `OpenShift Console Uri`. Кроме того, к главному узлу OpenShift можно подключиться с помощью следующей команды:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Удаление ресурсов

Если группа ресурсов, кластер OpenShift и все связанные ресурсы больше не нужны, их можно удалить с помощью команды [az group delete](/cli/azure/group#delete).

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок развертывания OpenShift в Azure](./openshift-troubleshooting.md)
- [Начало работы с OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
