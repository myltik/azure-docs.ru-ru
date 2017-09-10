---
title: "Создание правила Azure Load Balancer для кластера"
description: "Настройте Azure Load Balancer, чтобы открыть порты для кластера Azure Service Fabric."
services: service-fabric
documentationcenter: na
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/22/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 8496bd61d0133a428ce8e522faef5b538f19d4fc
ms.contentlocale: ru-ru
ms.lasthandoff: 08/25/2017

---

# <a name="open-ports-for-a-service-fabric-cluster"></a>Открытие портов для кластера Service Fabric

Подсистема балансировки нагрузки, развернутая вместе с кластером Azure Service Fabric, направляет трафик в ваше приложение, работающее на узле. Если изменить настройки приложения, чтобы использовать другой порт, необходимо предоставить этот порт в Azure Load Balancer (или направлять трафик через другой порт).

Подсистема балансировки нагрузки была создана автоматически при развертывании кластера Service Fabric в Azure. Если у вас нет подсистемы балансировки нагрузки, ознакомьтесь с разделом [Создание балансировщика нагрузки для Интернета на портале Azure](..\load-balancer\load-balancer-get-started-internet-portal.md).

## <a name="configure-service-fabric"></a>Настройка Service Fabric

Файл конфигурации **ServiceManifest.xml** приложения Service Fabric определяет конечные точки, которые будет использовать приложение. После обновления файла конфигурации для определения конечной точки необходимо обновить подсистему балансировки нагрузки, чтобы предоставить этот (или другой) порт. Дополнительные сведения о том, как создать конечную точку Service Fabric, см. в разделе [Указание ресурсов в манифесте службы](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки

Правило подсистемы балансировки нагрузки открывает порт с доступом к Интернету и перенаправляет трафик на порт внутреннего узла, используемый вашим приложением. Если у вас нет подсистемы балансировки нагрузки, ознакомьтесь с разделом [Создание балансировщика нагрузки для Интернета на портале Azure](..\load-balancer\load-balancer-get-started-internet-portal.md).

Чтобы создать правило подсистемы балансировки нагрузки, необходимо собрать следующие сведения:

- имя подсистемы балансировки нагрузки;
- группа ресурсов подсистемы балансировки нагрузки и кластера Service Fabric;
- внешний порт;
- внутренний порт.

## <a name="azure-cli"></a>Инфраструктура CLI Azure
>[!NOTE]
>Если требуется определить имя подсистемы балансировки нагрузки, выполните приведенную команду, чтобы быстро получить список всех подсистем балансировки нагрузки и связанных групп ресурсов.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>

Для создания правила подсистемы балансировки нагрузки требуется всего одна команда **Azure CLI**. Чтобы создать правило, необходимо знать имя подсистемы балансировки нагрузки и имя группы ресурсов.

```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

В команде Azure CLI используется несколько параметров, которые описаны в следующей таблице.

| Параметр | Описание |
| --------- | ----------- |
| `--backend-port`  | Порт, через который приложение Service Fabric ожидает передачи данных. |
| `--frontend-port` | Порт, который подсистема балансировки нагрузки предоставляет для внешних подключений. |
| `-lb-name` | Имя изменяемой подсистемы балансировки нагрузки. |
| `-g`       | Группа ресурсов, содержащая подсистему балансировки нагрузки и кластер Service Fabric. |
| `-n`       | Выбранное имя правила. |


>[!NOTE]
>Дополнительные сведения о создании подсистемы балансировки нагрузки с помощью Azure CLI см. в разделе [Создание балансировщика нагрузки для Интернета с помощью Azure CLI](..\load-balancer\load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

>[!NOTE]
>Если требуется определить имя подсистемы балансировки нагрузки, выполните приведенную команду, чтобы быстро получить список всех подсистем балансировки нагрузки и связанных групп ресурсов.
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

PowerShell немного сложнее в использовании, чем Azure CLI. По существу, чтобы создать правило, необходимо выполнить приведенные ниже действия.

1. Получите подсистему балансировки нагрузки из Azure.
2. Создайте правило.
3. Добавьте в подсистему балансировки нагрузки правило.
4. Обновите подсистему балансировки нагрузки.

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

В команде `New-AzureRmLoadBalancerRuleConfig` `-FrontendPort` представляет порт, который подсистема балансировки нагрузки предоставляет для внешних подключений, а `-BackendPort` представляет порт, через который приложение Service Fabric ожидает передачи данных.

>[!NOTE]
>Дополнительные сведения о создании подсистемы балансировки нагрузки с помощью PowerShell см. в разделе [Создание балансировщика нагрузки для Интернета в Resource Manager с помощью PowerShell](..\load-balancer\load-balancer-get-started-internet-arm-ps.md).


