---
title: "Масштабирование кластера ACS с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "Инструкции по масштабированию кластера службы контейнеров Azure с помощью интерфейса командной строки Azure."
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Mesos, Azure"
ms.assetid: 4a567474-f9a2-4172-bf86-7522aa4d4d80
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e8df2e68b1b7018d76da89ba9ab332b6ea216fb


---
# <a name="scale-an-azure-container-service"></a>Масштабирование службы контейнеров Azure
С помощью инструмента интерфейса командной строки Azure можно масштабировать число узлов службы контейнеров Azure (ACS). При масштабировании с помощью интерфейса командной строки Azure инструмент возвращает новый файл конфигурации, содержащий изменения, заданные контейнеру.

## <a name="about-the-command"></a>О команде
Для взаимодействия с контейнерами Azure интерфейс командной строки Azure должен находиться в режиме Azure Resource Manager. Чтобы переключиться в режим Resource Manager, вызовите `azure config mode arm`. У команды `acs` есть дочерняя команда с именем `scale`, выполняющая все операции масштабирования для службы контейнеров. Чтобы получить справку о различных параметрах, используемых в команде scale, выполните команду `azure acs scale --help`. Вывод будет примерно следующим:

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Использование команды для масштабирования
Для масштабирования службы контейнеров сначала необходимо определить **группу ресурсов** и **имя службы контейнеров Azure (ACS)**, а также задать новое число агентов. Используя меньшее или большее их количество, можно уменьшить или увеличить масштаб соответственно.

Перед масштабированием нужно узнать текущее количество агентов для службы контейнеров. Используйте команду `azure acs show <resource group> <ACS name>`, чтобы возвратить конфигурацию ACS. Обратите внимание на результаты параметра <mark>Count</mark>.

#### <a name="see-current-count"></a>Просмотр текущего количества
```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Масштабирование до нового количества
Очевидно, что службу контейнеров можно масштабировать, вызвав команду `azure acs scale` и указав **группу ресурсов**, **имя ACS** и **число агентов**. При масштабировании службы контейнеров интерфейс командной строки Azure возвращает строку JSON, представляющую новую конфигурацию службы контейнеров, включая измененное количество агентов.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Дальнейшие действия
* [Deploy a cluster](container-service-deployment.md) (Развертывание кластера)




<!--HONumber=Nov16_HO3-->


