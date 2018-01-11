---
title: "Обновление среды выполнения Azure Service Fabric | Документация Майкрософт"
description: "Узнайте, как использовать PowerShell для обновления среды выполнения кластера Service Fabric, размещенного в Azure."
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: faf134bc0952da913e90a93bc872a53f5f2369ff
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2017
---
# <a name="upgrade-the-runtime-of-a-service-fabric-cluster"></a>Обновление среды выполнения кластера Service Fabric

Это руководство является третьей частью цикла. В нем показано, как обновить среду выполнения Service Fabric в кластере Azure Service Fabric. Эта часть руководства предназначена для кластеров Service Fabric, работающих в Azure, и не относится к изолированным кластерам Service Fabric.

> [!WARNING]
> Для изучения этой части руководства требуется PowerShell. Обновление среды выполнения кластера еще не поддерживается инструментами Azure CLI. Кроме того, кластер можно обновить на портале. Дополнительные сведения см. в статье [Обновление кластера Azure Service Fabric](service-fabric-cluster-upgrade.md).

Если на кластере уже выполняется последняя версия среды выполнения Service Fabric, выполнять этот шаг не требуется. Однако эту статью можно использовать для установки любой поддерживаемой среды выполнения в кластере Azure Service Fabric.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Чтение версии кластера.
> * Настройка версии кластера.

Из этого цикла руководств вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * создание защищенного [кластера Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) или [кластера Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) в Azure;
> * [свертывание и развертывание кластера](service-fabric-tutorial-scale-cluster.md);
> * обновление среды выполнения кластера;
> * [развертывание службы управления API с помощью Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>Технические условия
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [модуль Azure PowerShell версии 4.1 или более поздней версии](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) либо [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Создайте защищенный [кластер Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) или [кластер Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) в Azure.
- Если вы развертываете кластер Windows, настройте среду разработки Windows. [Установите Visual Studio 2017](http://www.visualstudio.com), а также рабочие нагрузки **разработка Azure**, **ASP.NET и веб-разработка** и **кроссплатформенная разработка .NET Core**.  Теперь настройте [среду разработки .NET](service-fabric-get-started.md).
- Если вы развертываете кластер Linux, настройте среду разработки Java в [Linux](service-fabric-get-started-linux.md) или [MacOS](service-fabric-get-started-mac.md).  Установите [интерфейс командной строки Service Fabric](service-fabric-cli.md). 

### <a name="sign-in-to-azure"></a>Вход в Azure
Войдите в учетную запись Azure и выберите подписку, прежде чем выполнять команды Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Получение версии среды выполнения

После подключения к Azure и выбора подписки, содержащей кластер Service Fabric, можно узнать версию среды выполнения кластера.

```powershell
Get-AzureRmServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Можно также просто получить список всех кластеров в подписке с помощью следующей команды.

```powershell
Get-AzureRmServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Обратите внимание на значение **ClusterCodeVersion**. Оно будут использовано в следующем разделе.

## <a name="upgrade-the-runtime"></a>Обновление среды выполнения

Используйте значение **ClusterCodeVersion** из предыдущего раздела в командлете `Get-ServiceFabricRuntimeUpgradeVersion`, чтобы выяснить, какие целевые версии доступны для обновления. Этот командлет может быть выполнен только на компьютере, подключенном к Интернету. Например, если нужно узнать, до каких версий среды выполнения можно обновить версию `5.7.198.9494`, используйте следующую команду.

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Имея список версий, можно указать кластеру Azure Service Fabric обновить среду выполнения. Например, если версия `6.0.219.9494` доступна для обновления, выполните следующую команду, чтобы обновить кластер.

```powershell
Set-AzureRmServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> Обновление среды выполнения кластера может занять длительное время. PowerShell блокируется, пока выполняется обновление. Можно использовать другой сеанс PowerShell, чтобы проверить состояние обновления.

Состояние обновления можно отслеживать с помощью PowerShell или интерфейса командной строки `sfctl`.

Сначала подключитесь к кластеру с помощью SSL-сертификата, созданного в первой части этого руководства. Используйте командлет `Connect-ServiceFabricCluster` или `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Затем используйте командлет `Get-ServiceFabricClusterUpgrade` или `sfctl cluster upgrade-status` для отображения состояния. Ниже приведен примерный вид результата.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="conclusion"></a>Заключение
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Получение версии среды выполнения кластера.
> * Обновление среды выполнения кластера.
> * Отслеживание обновления.

Теперь перейдите к следующему руководству, из которого вы узнаете, как развернуть службу управления API с кластером Service Fabric.
> [!div class="nextstepaction"]
> [развертывание службы управления API с помощью Service Fabric](service-fabric-tutorial-deploy-api-management.md).
