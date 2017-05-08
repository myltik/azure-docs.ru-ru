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
ms.date: 01/05/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 32119a6ef586d616407c69e89a0d0f05758438bc
ms.lasthandoff: 04/27/2017


---
# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Связь между типами узлов Service Fabric и масштабируемыми наборами виртуальных машин
Масштабируемые наборы виртуальных машин относятся к вычислительным ресурсам Azure. Их можно использовать для развертывания коллекции виртуальных машин и управления ею как набором. Каждый тип узла, определенный в кластере Service Fabric, настроен как отдельный набор масштабирования виртуальных машин. Каждый тип узла поддерживает возможность независимого масштабирования, имеет разные наборы открытых портов и собственные метрики емкости.

На следующем снимке экрана показан кластер с двумя типами узлов: FrontEnd и BackEnd.  Каждый тип узла имеет пять узлов.

![Снимок экрана с двумя типами узлов][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Сопоставление экземпляров масштабируемых наборов ВМ с узлами
Как можно заметить на снимке выше, нумерация экземпляров масштабируемых наборов ВМ начинается с нуля и увеличивается. Нумерация отражается в именах. Например, BackEnd_0 является нулевым экземпляром масштабируемого набора ВМ BackEnd. Этот конкретный масштабируемый набор ВМ имеет пять экземпляров с именами BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 и BackEnd_4.

При увеличении масштаба масштабируемого набора ВМ создается новый экземпляр. Имя нового экземпляра масштабируемого набора ВМ обычно имеет следующий формат: имя масштабируемого набора ВМ + номер следующего экземпляра. В нашем примере это BackEnd_5.

## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Сопоставление балансировщиков нагрузки масштабируемых наборов ВМ с каждым типом узла или масштабируемым набором ВМ
Если вы развернули кластер на портале или использовали предоставленный пример шаблона Resource Manager, то при получении списка всех ресурсов в разделе группы ресурсов вы увидите балансировщики нагрузки для каждого типа узла или набора масштабируемых виртуальных машин.

Имя будет выглядеть следующим образом: **LB-&lt;имя типа узла&gt;**. Например, LB-sfcluster4doc-0, как показано на следующем снимке экрана:

![Ресурсы][Resources]

## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Удаленное подключение к экземпляру масштабируемого набора ВМ или узлу кластера
Каждый тип узла, определенный в кластере, настроен как отдельный набор масштабирования виртуальных машин.  Это означает, что типы узлов поддерживают независимое масштабирование и могут состоять из различных номеров SKU ВМ. В отличие от ВМ, состоящих из одного экземпляра, экземпляры масштабируемых наборов ВМ не получают собственные виртуальные IP-адреса. Поэтому поиск IP-адреса и порта, которые можно использовать для удаленного подключения к определенному экземпляру, может оказаться непростой задачей.

Ниже приведены действия по их обнаружению.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Шаг 1. Определение виртуального IP-адреса для типа узла и правил NAT для входящего трафика для RDP
Для этого необходимо получить значения правил NAT для входящего трафика, которые были определены в рамках определения ресурсов для **Microsoft.Network/loadBalancers**.

На портале перейдите к колонке балансировщика нагрузки и выберите **Параметры**.

![LBBlade][LBBlade]

В разделе **Параметры** щелкните **Правила NAT для входящего трафика**. Вы увидите IP-адрес и порт, которые можно использовать для удаленного подключения к первому экземпляру масштабируемого набора ВМ. На приведенном ниже снимке экрана это **104.42.106.156** и **3389**.

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Шаг 2. Определение порта, который можно использовать для удаленного подключения к конкретному экземпляру масштабируемого набора ВМ или узлу
Ранее в этом документе говорилось о сопоставлении экземпляров масштабируемого набора ВМ с узлами. Мы будем использовать эти данные для определения точного номера порта.

Порты распределяются по возрастанию номера экземпляра в наборе масштабирования виртуальных машин. Поэтому в примере с типом узла FrontEnd для каждого из пяти экземпляров будут использоваться указанные далее порты. Вам потребуется выполнить такое же сопоставление для своего экземпляра в наборе масштабирования виртуальных машин.

| **Экземпляр масштабируемого набора ВМ** | **Порт** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Шаг 3. Удаленное подключение к определенному экземпляру масштабируемого набора ВМ
На приведенном ниже снимке экрана использовалось подключение к удаленному рабочему столу для подключения к FrontEnd_1.

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Изменение значений диапазона портов RDP
### <a name="before-cluster-deployment"></a>Перед развертыванием кластера
При настройке кластера с помощью шаблона Resource Manager можно указать диапазон в **inboundNatPools**.

Перейдите к определению ресурса для **Microsoft.Network/loadBalancers**. В нем вы найдете описание **inboundNatPools**.  Замените значения *frontendPortRangeStart* и *frontendPortRangeEnd*.

![inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>После развертывания кластера
Это довольно сложный процесс, который может привести к перезапуску виртуальных машин. Теперь вам необходимо задать новые значения с помощью Azure PowerShell. Убедитесь, что на компьютере установлена среда Azure PowerShell 1.0 или более поздней версии. Если вы не сделали этого ранее, настоятельно рекомендуем выполнить инструкции в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview)

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

