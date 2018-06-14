---
title: Несколько виртуальных IP-адресов для облачной службы
description: Обзор данных об использовании нескольких виртуальных IP-адресов и установке нескольких виртуальных IP-адресов для облачной службы
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 85f6d26a-3df5-4b8e-96a1-92b2793b5284
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b6b7b0b2d7a7f33facaf72bbd2d7937364770673
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23020849"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Настройка нескольких виртуальных IP-адресов для облачной службы

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Доступ к облачным службам Azure через общедоступное подключение к Интернету можно получить с помощью IP-адреса, предоставляемого Azure. Этот общедоступный IP-адрес называется виртуальным IP-адресом, так как он привязан к подсистеме балансировки нагрузки Azure, а не к экземплярам виртуальной машины в облачной службе. Доступ к любому экземпляру виртуальной машины в облачной службе можно получить с помощью одного виртуального IP-адреса.

Однако существуют случаи, в которых может потребоваться несколько виртуальных IP-адресов в качестве точки входа для одной и той же облачной службы. Например, в вашей облачной службе может размещаться несколько веб-сайтов, которые требуют подключения по протоколу SSL с помощью порта 443 по умолчанию, так как каждый из веб-сайтов должен быть размещен для отдельного пользователя или клиента. В этом случае необходим отдельный общедоступный IP-адрес для каждого веб-сайта. На схеме ниже показан типичный мультитенантный веб-хостинг, требующий несколько сертификатов SSL для одного и того же общедоступного порта.

![Сценарий SSL с несколькими виртуальными IP-адресами](./media/load-balancer-multivip/Figure1.png)

В приведенном выше примере все виртуальные IP-адреса используют один и тот же общедоступный порт (443), а трафик перенаправляется на одну или несколько виртуальных машин с балансировкой нагрузки по уникальному частному порту для внутреннего IP-адреса облачной службы, в которой размещены все веб-сайты.

> [!NOTE]
> Другой вариант использования нескольких виртуальных IP-адресов заключается в размещении нескольких прослушивателей группы доступности SQL AlwaysOn в одном и том же наборе виртуальных машин.

Виртуальные IP-адреса динамичны по умолчанию. Это означает, что фактический IP-адрес, назначенный облачной службе, может со временем измениться. Чтобы избежать этого, можно зарезервировать виртуальный IP-адрес для службы. Дополнительную информацию о зарезервированных виртуальных IP-адресах см. в статье [Обзор зарезервированных IP-адресов](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Информацию о ценах на виртуальные IP-адреса и зарезервированные IP-адреса см. на странице [Цены на IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses/).

Вы можете использовать PowerShell для проверки виртуальных IP-адресов, используемых облачной службой, а также добавления и удаления виртуальных IP-адресов, привязки виртуального IP-адреса к конечной точке и настройки балансировки нагрузки для конкретного виртуального IP-адреса.

## <a name="limitations"></a>Ограничения

В настоящее время функциональность нескольких виртуальных IP-адресов ограничена следующими сценариями.

* **Только IaaS**. Вы можете включить несколько виртуальных IP-адресов для облачных служб, содержащих виртуальные машины. Нельзя использовать несколько виртуальных IP-адресов в сценариях PaaS с экземплярами ролей.
* **Только PowerShell**. Несколькими виртуальными IP-адресами можно управлять только с помощью PowerShell.

Эти ограничения являются временными и могут измениться в любое время. Обязательно вернитесь на эту страницу, чтобы проверить будущие изменения.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Как добавить виртуальный IP-адрес в облачную службу
Чтобы добавить виртуальный IP-адрес в службу, выполните следующую команду PowerShell:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Эта команда отображает примерно такой результат:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Как удалить виртуальный IP-адрес из облачной службы
Чтобы удалить виртуальный IP-адрес, добавленный в службу в примере выше, выполните следующую команду PowerShell:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Виртуальный IP-адрес можно удалить только в том случае, если с ним не связаны конечные точки.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Как получить данные о виртуальном IP-адресе из облачной службы
Чтобы получить виртуальный IP-адрес, связанный с облачной службой, выполните следующий сценарий PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Сценарий отображает примерно такой результат:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

В этом примере облачная служба имеет 3 виртуальных IP-адреса:

* **Vip1** — виртуальный IP-адрес по умолчанию, так как для параметра IsDnsProgrammedName задано значение true;
* **Vip2** и **Vip3** не используются, так как они не имеют IP-адресов. Они будут использоваться, только если связать конечную точку с виртуальным IP-адресом.

> [!NOTE]
> За использование дополнительных виртуальных IP-адресов в подписке начнет взиматься плата, когда они будут связаны с конечной точкой. Дополнительную информацию о ценах см. на странице [Цены на IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Как привязать виртуальный IP-адрес к конечной точке

Чтобы связать виртуальный IP-адрес в облачной службе с конечной точкой, выполните следующую команду PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Эта команда создает конечную точку, связанную с виртуальным IP-адресом, с именем *Vip2* на порте *80* и связывает ее с виртуальной машиной с именем *myVM1* в облачной службе с именем *myService* с помощью *TCP* на порте *8080*.

Чтобы проверить конфигурацию, выполните следующую команду PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Результат должен быть аналогичным приведенному ниже:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Как включить балансировку нагрузки в конкретном виртуальном IP-адресе

Вы можете связать один виртуальный IP-адрес с несколькими виртуальными машинами для балансировки нагрузки. Например, у вас есть облачная служба с именем *myService* и две виртуальные машины с именами *myVM1* и *myVM2*. Облачная служба имеет несколько виртуальных IP-адресов, имя одного из которых — *Vip2*. Если вы хотите убедиться, что весь трафик порта *81* по адресу *Vip2* распределяется между *myVM1* и *myVM2* на порте *8181*, выполните следующий сценарий PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Вы можете также обновить подсистему балансировки нагрузки, чтобы использовать другой виртуальный IP-адрес. Например, если выполнить следующую команду PowerShell, набор балансировки нагрузки станет использовать виртуальный IP-адрес с именем Vip1:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Дальнейшие действия

[Служба анализа журналов для балансировщика нагрузки Azure (предварительная версия)](load-balancer-monitor-log.md)

[Обзор подсистемы балансировки нагрузки, доступной в Интернете](load-balancer-internet-overview.md)

[Приступая к работе с подсистемой балансировки нагрузки, доступной в Интернете](load-balancer-get-started-internet-arm-ps.md)

[Обзор виртуальной сети](../virtual-network/virtual-networks-overview.md)

[API REST зарезервированных IP-адресов](https://msdn.microsoft.com/library/azure/dn722420.aspx)
