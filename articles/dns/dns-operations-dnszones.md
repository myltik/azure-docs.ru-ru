---
title: "Управление зонами DNS с помощью PowerShell | Документация Майкрософт"
description: "Зонами DNS можно управлять с помощью Azure Powershell. Как обновлять, удалять и создавать зоны DNS в Azure DNS"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1136b0e378cec14a9c899b61ec1dd21ff6720ba5

---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Как управлять зонами DNS с помощью PowerShell

> [!div class="op_single_selector"]
> * [Интерфейс командной строки Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

В этой статье описывается, как управлять зоной DNS с помощью PowerShell. Чтобы выполнить описываемые действия, вам потребуется установить последнюю версию командлетов PowerShell Azure Resource Manager (1.0 или более позднюю версию). Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="create-a-new-dns-zone"></a>Создание зоны DNS

Сведения о создании зоны DNS см. в [этой статье](dns-getstarted-create-dnszone.md).

## <a name="get-a-dns-zone"></a>Получение зоны DNS

Чтобы получить зону DNS, используйте командлет `Get-AzureRmDnsZone` . Эта операция возвращает объект зоны DNS, соответствующий существующей зоне в Azure DNS. Объект содержит данные о зоне (например, число наборов записей), но не сами наборы записей.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
```

## <a name="list-dns-zones"></a>Перечисление зон DNS

Если опустить имя зоны в командлете `Get-AzureRmDnsZone`, можно получить список всех зон в группе ресурсов. Эта операция возвращает массив объектов зоны.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

## <a name="update-a-dns-zone"></a>Обновление зоны DNS

Изменить ресурс зоны DNS можно с помощью командлета `Set-AzureRmDnsZone`. При этом ни один набор записей DNS в пределах зоны не обновляется (см. статью [Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets.md)). Эта команда используется только для обновления свойств самого ресурса зоны. В текущее время область применения ограничена "тегами" диспетчера ресурсов Azure для ресурса зоны. Подробные сведения см. в разделе [Об Etag и тегах](dns-getstarted-create-dnszone.md#tagetag).

Используйте один из следующих двух способов для обновления зоны DNS.

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Укажите зону с помощью имени и группы ресурсов.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]
```

### <a name="specify-the-zone-using-a-zone-object"></a>Укажите зону с помощью объекта $zone.

Укажите зону с помощью объекта $zone, полученного с помощью `Get-AzureRmDnsZone`. При использовании командлета `Set-AzureRmDnsZone` с объектом $zone будут применены проверки Etag, чтобы параллельные изменения не были перезаписаны. Для подавления этих проверок укажите необязательный параметр *-Overwrite*. Подробные сведения см. в разделе [Об Etag и тегах](dns-getstarted-create-dnszone.md#tagetag).

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
<..modify $zone.Tags here...>
Set-AzureRmDnsZone -Zone $zone [-Overwrite]
```

## <a name="delete-a-dns-zone"></a>Удаление зоны DNS

Зоны DNS записей можно удалить с помощью командлета Remove-AzureRmDnsZone.

Прежде чем удалить зону DNS в Azure DNS, необходимо удалить все наборы записей, за исключением записей NS и SOA в корне зоны, которые были созданы автоматически вместе с зоной.

Используйте один из следующих двух способов для удаления зоны DNS.

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Укажите зону с помощью имени зоны и имени группы ресурсов.

Для этой операции можно использовать необязательный параметр `-Force`, который подавляет запрос на подтверждение удаления зоны DNS.

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-zone-using-a-zone-object"></a>Укажите зону с помощью объекта $zone.

Укажите зону с помощью объекта $zone, полученного с помощью `Get-AzureRmDnsZone`. Для этой операции можно использовать необязательный параметр `-Force`, который подавляет запрос на подтверждение удаления зоны DNS. Как и для командлета `Set-AzureRmDnsZone`, при указании набора записей с помощью объекта $zone вы включаете проверки Etag, чтобы убедиться, что параллельные изменения не удаляются.

Необязательный параметр `-Overwrite` позволяет не выполнять эти проверки. Подробные сведения см. в разделе [Об Etag и тегах](dns-getstarted-create-dnszone.md#tagetag).

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]
```

Объект зоны также можно направить в конвейер, а не передавать в качестве параметра:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]
```

## <a name="next-steps"></a>Дальнейшие действия

Создав зону DNS, создайте [наборы записей и записи](dns-getstarted-create-recordset.md) , чтобы начать разрешение имен для домена Интернета.




<!--HONumber=Dec16_HO2-->


