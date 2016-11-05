---
title: Управление зонами DNS с помощью PowerShell | Microsoft Docs
description: Зонами DNS можно управлять с помощью Azure Powershell. Как обновлять, удалять и создавать зоны DNS в Azure DNS
services: dns
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: cherylmc

---
# Как управлять зонами DNS с помощью PowerShell
> [!div class="op_single_selector"]
> * [Интерфейс командной строки Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)
> 
> 

В этой статье описывается, как управлять зоной DNS с помощью PowerShell. Чтобы выполнить описываемые действия, вам потребуется установить последнюю версию командлетов PowerShell Azure Resource Manager (1.0 или более позднюю версию). Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Создание зоны DNS
Сведения о создании зоны DNS см. в разделе [Приступая к работе с Azure DNS с помощью PowerShell](dns-getstarted-create-dnszone.md).

## Получение зоны DNS
Чтобы получить зону DNS, используйте командлет `Get-AzureRmDnsZone`. Эта операция возвращает объект зоны DNS, соответствующий существующей зоне в Azure DNS. Объект содержит данные о зоне (например, число наборов записей), но не сами наборы записей.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## Перечисление зон DNS
Если опустить имя зоны в командлете `Get-AzureRmDnsZone`, можно получить список всех зон в группе ресурсов. Эта операция возвращает массив объектов зоны.

    $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## Обновление зоны DNS
Изменить ресурс зоны DNS можно с помощью командлета `Set-AzureRmDnsZone`. При этом ни один набор записей DNS в пределах зоны не обновляется (см. [Управление записями DNS](dns-operations-recordsets.md)). Эта команда используется только для обновления свойств самого ресурса зоны. В текущее время область применения ограничена "тегами" диспетчера ресурсов Azure для ресурса зоны. Подробные сведения см. в разделе [Etag и теги](dns-getstarted-create-dnszone.md#Etags-and-tags).

Используйте один из следующих двух способов для обновления зоны DNS.

### Укажите зону с помощью имени и группы ресурсов.
    Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Укажите зону с помощью объекта $zone.
Укажите зону с помощью объекта $zone, полученного с помощью `Get-AzureRmDnsZone`. При использовании командлета `Set-AzureRmDnsZone` с объектом $zone будут применены проверки Etag, чтобы параллельные изменения не были перезаписаны. Для подавления этих проверок укажите параметр *-Overwrite*. Подробные сведения см. в разделе [Etag и теги](dns-getstarted-create-dnszone.md#Etags-and-tags).

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    <..modify $zone.Tags here...>
    Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## Удаление зоны DNS
Зоны DNS записей можно удалить с помощью командлета Remove-AzureRmDnsZone.

Прежде чем удалить зону DNS в Azure DNS, необходимо удалить все наборы записей, за исключением записей NS и SOA в корне зоны, которые были созданы автоматически вместе с зоной.

Используйте один из следующих двух способов для удаления зоны DNS.

### Укажите зону с помощью имени зоны и имени группы ресурсов.
Для этой операции можно использовать необязательный параметр *-Force*, который подавляет запрос подтверждения на удаление зоны DNS.

    Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

### Укажите зону с помощью объекта $zone.
Укажите зону с помощью объекта $zone, полученного с помощью `Get-AzureRmDnsZone`. Для этой операции можно использовать необязательный параметр *-Force*, который подавляет запрос подтверждения на удаление зоны DNS. Как и для командлета `Set-AzureRmDnsZone`, при указании набора записей с помощью объекта $zone вы включаете проверки Etag, чтобы убедиться, что параллельные изменения не удаляются. <BR> Необязательный параметр *-Overwrite* подавляет эти проверки. Подробные сведения см. в разделе [Etag и теги](dns-getstarted-create-dnszone.md#Etags-and-tags).

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



Объект зоны также можно направить в конвейер, а не передавать в качестве параметра:

    Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## Дальнейшие действия
Создав зону DNS, создайте [наборы записей и записи](dns-getstarted-create-recordset.md), чтобы начать разрешение имен для домена Интернета.

<!---HONumber=AcomDC_0817_2016-->