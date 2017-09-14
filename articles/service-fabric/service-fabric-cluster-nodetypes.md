---
title: "Типы узлов и масштабируемые наборы виртуальных машин в Service Fabric | Документация Майкрософт"
description: "Описание связи типов узлов с масштабируемыми наборами ВМ в Service Fabric и сведения о процедуре удаленного подключения к экземпляру масштабируемого набора ВМ или узлу кластера."
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
ms.date: 06/05/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 8c9e91d122591a19d34d944e2d9aaeb327cdafe4
ms.contentlocale: ru-ru
ms.lasthandoff: 09/05/2017

---
# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Связь между типами узлов Service Fabric и масштабируемыми наборами виртуальных машин
Масштабируемые наборы виртуальных машин являются вычислительными ресурсами Azure. Их можно использовать для развертывания коллекции виртуальных машин и управления ею как набором. Каждый тип узла, определенный в кластере Service Fabric, настроен как отдельный набор масштабирования виртуальных машин. Каждый тип узла поддерживает возможность независимого масштабирования, имеет разные наборы открытых портов и собственные метрики емкости.

На следующем снимке экрана показан кластер с двумя типами узлов: FrontEnd и BackEnd.  Каждый тип узла имеет пять узлов.

![Снимок экрана с двумя типами узлов][NodeTypes]

## <a name="mapping-virtual-machine-scale-set-instances-to-nodes"></a>Сопоставление экземпляров масштабируемых наборов виртуальных машин с узлами
Как можно заметить на снимке выше, нумерация экземпляров масштабируемых наборов виртуальных машин начинается с нуля и увеличивается. Нумерация отражается в именах. Например, BackEnd_0 является нулевым экземпляром масштабируемого набора виртуальных машин BackEnd. Этот конкретный масштабируемый набор виртуальных машин имеет пять экземпляров с именами BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 и BackEnd_4.

При увеличении масштаба масштабируемого набора виртуальных машин создается новый экземпляр. Имя нового экземпляра масштабируемого набора виртуальных машин обычно имеет следующий формат: имя масштабируемого набора виртуальных машин + номер следующего экземпляра. В нашем примере это BackEnd_5.

## <a name="mapping-virtual-machine-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Сопоставление балансировщиков нагрузки масштабируемых наборов виртуальных машин с каждым типом узла или масштабируемым набором виртуальных машин
Если вы развернули кластер из портала или использовали образец шаблона Resource Manager, в колонке группы ресурсов будет содержаться список всех ресурсов. Вы увидите балансировщики нагрузки для каждого набора масштабирования виртуальных машин или типа узла.

Имя будет выглядеть следующим образом: **LB-&lt;имя типа узла&gt;**. Например, LB-sfcluster4doc-0, как показано на следующем снимке экрана:

![Ресурсы][Resources]

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Удаленное подключение к экземпляру масштабируемого набора виртуальных машин или узлу кластера
Каждый тип узла, определенный в кластере, настроен как отдельный масштабируемый набор виртуальных машин.  Это означает, что типы узлов поддерживают независимое масштабирование. Они могут состоять из различных номеров SKU виртуальной машины. В отличие от виртуальных машин, состоящих из одного экземпляра, экземпляры масштабируемых наборов виртуальных машин не получают собственные виртуальные IP-адреса. Поэтому поиск IP-адреса и порта, которые можно использовать для удаленного подключения к определенному экземпляру, может оказаться непростой задачей.

Ниже приведены действия по их обнаружению.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Шаг 1. Определение виртуального IP-адреса для типа узла и правил NAT для входящего трафика для RDP
Для этого необходимо получить значения правил NAT для входящего трафика, которые были определены в рамках определения ресурсов для **Microsoft.Network/loadBalancers**.

На портале перейдите к колонке балансировщика нагрузки и выберите **Параметры**.

![LBBlade][LBBlade]

В разделе **Параметры** щелкните **Правила NAT для входящего трафика**. Вы увидите IP-адрес и порт, которые можно использовать для удаленного подключения к первому экземпляру масштабируемого набора виртуальных машин. На приведенном ниже снимке экрана это **104.42.106.156** и **3389**.

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-virtual-machine-scale-set-instancenode"></a>Шаг 2. Определение порта, который можно использовать для удаленного подключения к конкретному экземпляру масштабируемого набора виртуальных машин или узлу
Ранее в этом документе говорилось о сопоставлении экземпляров масштабируемого набора виртуальных машин с узлами. Мы используем эти данные для определения точного номера порта.

Порты выделяются по возрастанию номера экземпляра в масштабируемом наборе виртуальных машин. Поэтому в примере с типом узла FrontEnd для каждого из пяти экземпляров будут использоваться указанные далее порты. Вам потребуется выполнить такое же сопоставление для своего экземпляра в масштабируемом наборе виртуальных машин.

| **Экземпляр масштабируемого набора виртуальных машин** | **Порт** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

### <a name="step-3-remote-connect-to-the-specific-virtual-machine-scale-set-instance"></a>Шаг 3. Удаленное подключение к определенному экземпляру масштабируемого набора виртуальных машин
На приведенном ниже снимке экрана использовалось подключение к удаленному рабочему столу для подключения к FrontEnd_1.

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Изменение значений диапазона портов RDP
### <a name="before-cluster-deployment"></a>Перед развертыванием кластера
При настройке кластера с помощью шаблона Resource Manager можно указать диапазон в **inboundNatPools**.

Перейдите к определению ресурса для **Microsoft.Network/loadBalancers**. В нем вы найдете описание **inboundNatPools**.  Замените значения *frontendPortRangeStart* и *frontendPortRangeEnd*.

![inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>После развертывания кластера
Это довольно сложный процесс, который может привести к перезапуску виртуальных машин. Задайте новые значения с помощью Azure PowerShell. Убедитесь, что на компьютере установлена среда Azure PowerShell 1.0 или более поздней версии. Если вы не используете Azure Powershell 1.0 или более позднюю версию, настоятельно рекомендуем выполнить инструкции в статье [Общие сведения об Azure PowerShell](/powershell/azure/overview).

Войдите в учетную запись Azure. Если команды PowerShell по какой-то причине завершаются неудачно, проверьте, правильно ли установлен Azure PowerShell.

```
Login-AzureRmAccount
```

Выполните приведенную ниже команду для получения сведений о балансировщике нагрузки, и вы увидите значения и описание **inboundNatPools**.

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Задайте для *frontendPortRangeEnd* и *frontendPortRangeStart* требуемые значения.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```

## <a name="how-to-change-the-rdp-username--password-for-nodes"></a>Как изменить имя пользователя и пароль для входа по протоколу удаленного рабочего стола (RDP) для узлов

Ниже описывается, как изменить пароль для всех узлов определенного типа. Эти изменения будут применены ко всем имеющимся и будущим узлам в масштабируемом наборе виртуальных машин.

### <a name="step-1-open-powershell-with-elevated-privileges-administrator-mode"></a>Шаг 1. Откройте PowerShell с более высоким уровнем привилегий (режим администратора). 
### <a name="step-2-run-the-following-commands-to-log-in-and-select-your-subscription-for-the-session-change-the-subscriptionid-parameter-to-your-subscription-id"></a>Шаг 2. Выполните следующие команды, чтобы войти в систему, и выберите подписку для сеанса. Замените параметр `SUBSCRIPTIONID` своим идентификатором подписки. 

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
```

### <a name="step-3-run-the-following-script-with-the-appropriate-nodetypename-resourcegroup-username-and-password-values-the-username-and-password-values-will-be-the-new-credentials-that-should-be-used-in-future-rdp-sessions"></a>Шаг 3. Запустите следующий скрипт, используя соответствующие значения `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME` и `PASSWORD`. Значения `USERNAME` и `PASSWORD` будут использоваться как новые учетные данные в будущих сеансах RDP. 

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

## <a name="next-steps"></a>Дальнейшие действия
* [Общие сведения о функции "Развертывание в любом месте" и сравнение кластеров под управлением Azure](service-fabric-deploy-anywhere.md)
* [Безопасность кластера](service-fabric-cluster-security.md)
* [ Пакет SDK для Service Fabric и начало работы](service-fabric-get-started.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

