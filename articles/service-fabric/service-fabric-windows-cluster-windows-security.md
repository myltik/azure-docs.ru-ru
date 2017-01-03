---
title: "Защита кластера, работающего под управлением Windows, с помощью системы безопасности Windows | Документация Майкрософт"
description: "Узнайте, как настроить безопасность обмена данными между узлами или между клиентом и узлом в автономном кластере, работающем под управлением Windows, с помощью системы безопасности Windows."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/09/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 08024aedb0889c91909f12aa1e38c91b11d3090f
ms.openlocfilehash: 142b76f053adc273e2e071f169f8f647fbd1c241


---
# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Защита автономного кластера под управлением Windows с помощью системы безопасности Windows
Во избежание несанкционированного доступа к кластеру Service Fabric следует защитить его, особенно если на нем выполняются рабочие нагрузки. В этой статье описывается, как настроить безопасный обмен данными между узлами, а также между клиентом и узлом с помощью системы безопасности Windows в файле *ClusterConfig.JSON*. Это соответствует шагу по настройке безопасности в статье [Создание кластера под управлением Windows Server и управление им](service-fabric-cluster-creation-for-windows-server.md). Дополнительные сведения об использовании системы безопасности Windows в Service Fabric см. в статье [Сценарии защиты кластера Service Fabric](service-fabric-cluster-security.md).

> [!NOTE]
> Выбирать вариант безопасности для защиты обмена данными между узлами следует тщательно, так как один вариант безопасности кластера нельзя обновить до другого. Чтобы изменить вариант безопасности, необходимо перестроить весь кластер.
> 
> 

## <a name="configure-windows-security"></a>Настройка безопасности Windows
В примере файла конфигурации *ClusterConfig.Windows.JSON*, скачанном с пакетом автономного кластера [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690), содержится шаблон для настройки безопасности Windows.  Безопасность Windows настраивается в разделе **Properties** .

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **Параметр конфигурации** | **Описание** |
| --- | --- |
| ClusterCredentialType |Безопасность Windows можно включить, задав для параметра **ClusterCredentialType** значение *Windows*. |
| ServerCredentialType |Безопасность Windows для клиентов можно включить, задав для параметра **ServerCredentialType** значение *Windows*. Это указывает, что клиенты кластера и сам кластер работают внутри домена Active Directory. |
| WindowsIdentities |Содержит удостоверения кластера и клиента. |
| ClusterIdentity |Настраивает безопасность обмена данными между узлами. Имя группы компьютеров. |
| ClientIdentities |Настраивает безопасность обмена данными между клиентами и узлами. Массив учетных записей клиентов. |
| Удостоверение |Удостоверение клиента, пользователь домена. |
| IsAdmin |Значение true указывает, что у пользователя домена есть клиентский доступ с правами администратора, а значение false — клиентский доступ с правами пользователя. |

[Безопасный обмен данными между узлами](service-fabric-cluster-security.md#node-to-node-security) можно настроить с помощью параметра **ClusterIdentity**. Чтобы создать отношения доверия между узлами, им нужно сообщить друг о друге. Это можно сделать, создав группу домена со всеми узлами в кластере. Имя этой группы необходимо указать в параметре **ClusterIdentity**. Дополнительные сведения см. в статье [How to Create a Group in Active Directory](https://msdn.microsoft.com/en-us/library/aa545347(v=cs.70).aspx) (Как создать группу в Active Directory).

[Безопасность обмена данными между клиентом и узлом](service-fabric-cluster-security.md#client-to-node-security) настраивается с помощью параметра **ClientIdentities**. Чтобы установить отношение доверия между клиентом и кластером, необходимо настроить кластер таким образом, чтобы ему были известны удостоверения клиентов, которым можно доверять. Это можно сделать двумя разными способами: указав пользователей группы домена или узла домена, которые могут подключаться. Service Fabric поддерживает два разных типа контроля доступа для клиентов, подключенных к кластеру Service Fabric: администраторский и пользовательский. Благодаря контролю доступа администратор кластера может ограничить доступ разных групп пользователей на выполнение определенных типов операций в кластере, повысив тем самым уровень безопасности кластера.  Администраторы имеют полный доступ к возможностям управления (включая возможности чтения и записи). Пользователи по умолчанию имеют доступ только на чтение для управления (например, при работе с запросами) и возможность разрешения приложений и служб.

В следующем примере кода раздел **security** настраивает систему безопасности Windows и указывает, что компьютеры в группе компьютеров *ServiceFabric\\ClusterNodes* входят в этот кластер, а также что *CONTOSO\usera* имеет клиентский доступ с правами администратора.

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric\\ClusterNodes",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>Дальнейшие действия
После настройки безопасности Windows в файле *ClusterConfig.JSON* возобновите процесс создания кластера, который описан в разделе [Создание кластера под управлением Windows Server и управление им](service-fabric-cluster-creation-for-windows-server.md).

Дополнительные сведения об обеспечении безопасного обмена данными между узлами, между клиентом и узлом и об управлении доступом на основе ролей см. в статье [Сценарии защиты кластера Service Fabric](service-fabric-cluster-security.md).

Примеры подключений с помощью PowerShell или FabricClient см. в статье [Безопасное подключение к кластеру без AAD](service-fabric-connect-to-secure-cluster.md).




<!--HONumber=Nov16_HO4-->


