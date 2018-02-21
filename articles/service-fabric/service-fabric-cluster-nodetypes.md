---
title: "Типы узлов Azure Service Fabric и масштабируемые наборы виртуальных машин | Документация Майкрософт"
description: "Узнайте, как типы узлов Azure Service Fabric связаны с масштабируемыми наборами виртуальных машин и как удаленно подключаться к экземплярам масштабируемых наборов или узлам кластеров."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/5/2018
ms.author: chackdan
ms.openlocfilehash: 720bb83c9d8540549852ce78ee1709f8c8717348
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Типы узлов Azure Service Fabric и масштабируемые наборы виртуальных машин
Масштабируемые наборы виртуальных машин являются вычислительными ресурсами Azure. Их можно использовать для развертывания коллекций виртуальных машин и управления ими в качестве набора. Настройте отдельный масштабируемый набор для каждого типа узла, определенного в кластере Azure Service Fabric. Все типы узлов можно масштабировать независимо друг от друга, открывать разные наборы портов и использовать различные метрики производительности.

На следующем рисунке показан кластер с двумя типами узлов, которые называются FrontEnd и BackEnd. Каждый тип узла имеет пять узлов.

![Кластер с двумя типами узлов][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Сопоставление экземпляров масштабируемых наборов виртуальных машин с узлами
Как показано на предыдущем рисунке, экземпляры масштабируемого набора начинаются с экземпляра 0, а затем увеличиваются на 1. Нумерация узлов отражается в именах. Например, узел BackEnd_0 является нулевым экземпляром масштабируемого набора BackEnd. Этот конкретный масштабируемый набор имеет пять экземпляров с именами BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 и BackEnd_4.

При увеличении масштаба масштабируемого набора создается экземпляр. Имя нового экземпляра масштабируемого набора, как правило, состоит из имени масштабируемого набора и номера следующего экземпляра. В нашем примере это BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Сопоставление балансировщиков нагрузки масштабируемых наборов с типами узлов и масштабируемыми наборами
При развертывании кластера на портале Azure или использовании примера шаблона Azure Resource Manager перечисляются все ресурсы в группе ресурсов. Отображаются балансировщики нагрузки для каждого масштабируемого набора или типа узла. Имя балансировщика нагрузки имеет следующий формат: **LB-&lt;имя типа узла&gt;**, например LB-sfcluster4doc-0, как показано на следующем рисунке:

![Ресурсы][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Удаленное подключение к экземпляру масштабируемого набора виртуальных машин или узлу кластера
Настройте отдельный масштабируемый набор для каждого типа узла, определенного в кластере. Типы узлов можно масштабировать независимо друг от друга. Кроме того, можно использовать разные номера SKU виртуальных машин. В отличие от одноэкземплярных виртуальных машин, экземпляры масштабируемых наборов не имеют собственных виртуальных IP-адресов. Поэтому поиск IP-адреса и порта, которые можно использовать для удаленного подключения к определенному экземпляру, может оказаться непростой задачей.

Чтобы найти IP-адрес и порт, которые можно использовать для удаленного подключения к определенному экземпляру, выполните следующие действия.

**Шаг 1.** Найдите виртуальные IP-адреса для каждого типа узла, получив правила преобразования сетевых адресов для входящих подключений для протокола удаленного рабочего стола (RDP).

Сначала необходимо получить значения правил преобразования сетевых адресов для входящих подключений, определенные при определении ресурса для `Microsoft.Network/loadBalancers`.

На портале Azure на странице балансировщика нагрузки выберите **Параметры** > **Правила NAT для входящего трафика**. Вы увидите IP-адрес и порт, которые можно использовать для удаленного подключения к первому экземпляру масштабируемого набора. 

![Подсистема балансировки нагрузки][LBBlade]

На следующем рисунке показан IP-адрес **104.42.106.156** и порт **3389**.

![Правила преобразования сетевых адресов][NATRules]

**Шаг 2.** Найдите порт, который можно использовать для удаленного подключения к конкретному экземпляру или узлу масштабируемого набора.

Экземпляры масштабируемых наборов сопоставляются с узлами. Информация о масштабируемом наборе позволяет точно определить порт, который нужно использовать.

Порты выделяются в возрастающем порядке в соответствии с экземпляром масштабируемого набора. Для приведенного выше типа узла FrontEnd в следующей таблице указаны порты для каждого из пяти экземпляров узла. Применяйте к экземпляру масштабируемого набора одно то же сопоставление.

| **Экземпляр масштабируемого набора виртуальных машин** | **Порт** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

**Шаг 3.** Выполните удаленное подключение к определенному экземпляру масштабируемого набора.

На следующем рисунке показано подключение к экземпляру масштабируемого набора FrontEnd_1 с использованием подключения к удаленному рабочему столу:

![Подключение к удаленному рабочему столу][RDP]

## <a name="change-the-rdp-port-range-values"></a>Изменение значений диапазона портов RDP

### <a name="before-cluster-deployment"></a>Перед развертыванием кластера
При настройке кластера с помощью шаблона Resource Manager укажите диапазон в `inboundNatPools`.

Перейдите к определению ресурса для `Microsoft.Network/loadBalancers`. Найдите описание `inboundNatPools`.  Замените значения `frontendPortRangeStart` и `frontendPortRangeEnd`.

![Значения inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>После развертывания кластера
Изменить значения диапазона портов RDP после развертывания кластера гораздо сложнее. Чтобы убедиться, что виртуальные машины не перезапускаются, задайте новые значения с помощью Azure PowerShell. 

> [!NOTE]
> Убедитесь, что на компьютере установлена среда Azure PowerShell версии 1.0 или более поздней. Если у вас нет Azure PowerShell версии 1.0 или более поздней, мы советуем выполнить действия, описанные в статье [Общие сведения об Azure PowerShell](/powershell/azure/overview).

1. Войдите в учетную запись Azure. Если следующая команда PowerShell завершится ошибкой, убедитесь, что среда PowerShell установлена правильно.

    ```
    Login-AzureRmAccount
    ```

2. Чтобы получить подробные сведения о балансировщике нагрузки и просмотреть значения в описании `inboundNatPools`, выполните следующий код:

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. Задайте для `frontendPortRangeEnd` и `frontendPortRangeStart` необходимые значения.

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>Изменение имени пользователя и пароля удаленного рабочего стола для узлов

Чтобы изменить пароль для всех узлов определенного типа, сделайте следующее. Эти изменения будут применены ко всем имеющимся и будущим узлам в масштабируемом наборе.

1. Откройте PowerShell от имени администратора. 
2. Чтобы войти в систему и выбрать подписку для сеанса, выполните следующие команды. Замените параметр `SUBSCRIPTIONID` своим идентификатором подписки. 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. Выполните следующий сценарий. Используйте соответствующие значения `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME` и `PASSWORD`. `USERNAME` и `PASSWORD` — это новые учетные данные, используемые в будущих сеансах удаленного рабочего стола. 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о возможности развертывания в любом месте и сравнении с кластерами под управлением Azure см. в статье [Создание кластеров Service Fabric в Windows Server или Linux](service-fabric-deploy-anywhere.md).
* Дополнительные сведения о безопасности кластеров см. в статье [Сценарии защиты кластера Service Fabric](service-fabric-cluster-security.md).
* Дополнительные сведения пакете SDK Service Fabric см. в статье [Подготовка среды разработки](service-fabric-get-started.md).

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
