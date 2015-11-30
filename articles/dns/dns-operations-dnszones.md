<properties 
   pageTitle="Операции с зонами DNS | Microsoft Azure" 
   description="Зонами DNS можно управлять с помощью Azure Powershell. Как обновлять, удалять и создавать зоны DNS в Azure DNS" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/10/2015"
   ms.author="joaoma"/>

# Как управлять зонами DNS с помощью PowerShell

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)


В этом руководстве показано, как управлять зоной DNS. Оно поможет вам понять последовательность операций для администрирования зоны DNS.

## Создание зоны DNS

Чтобы создать зону DNS для размещения домена, используйте командлет New-AzureDnsZone:

		PS C:\> $zone = New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [–Tag $tags] 

Эта операция создает новую зону DNS в службе Azure DNS и возвращает локальный объект, соответствующий этой зоне. При необходимости можно указать массив тегов диспетчера ресурсов Azure. Дополнительные сведения см. в разделе [Etag и теги](../dns-getstarted-create-dnszone.md#Etags-and-tags).

Имя зоны должно быть уникальным в пределах группы ресурсов, а зона не должна существовать, иначе операция завершится ошибкой.

То же имя зоны можно использовать повторно в другой группе ресурсов или другой подписке Azure. Если имеется несколько зон с одним именем, каждому экземпляру будут назначены разные адреса серверов имен, и только один экземпляр может быть делегирован из родительского домена. Подробнее: [Делегирование домена Azure DNS](dns-domain-delegation.md).

## Получение зоны DNS

Чтобы получить зону DNS, используйте командлет Get-AzureDnsZone:

		PS C:\> $zone = Get-AzureDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Эта операция возвращает объект зоны DNS, представляющий существующую зону в Azure DNS. Объект содержит данные о зоне (например, число наборов записей), но не сами наборы записей.

## Перечисление зон DNS
Если опустить имя зоны в командлете Get-AzureDnsZone, можно получить список всех зон в группе ресурсов:

	PS C:\> $zoneList = Get-AzureDnsZone -ResourceGroupName MyAzureResourceGroup
Эта операция возвращает массив объектов зоны.

## Обновление зоны DNS
Изменить ресурс зоны DNS можно с помощью командлета Set-AzureDnsZone. При этом ни один набор записей DNS в пределах зоны не обновляется (см. [Управление записями DNS](dns-operations-recordsets.md)). Эта команда используется только для обновления свойств самого ресурса зоны. В текущее время область применения ограничена "тегами" диспетчера ресурсов Azure для ресурса зоны. Подробные сведения см. в разделе [Etag и теги](dns-getstarted-create-dnszone.md#Etags-and-tags).

Используйте один из следующих двух способов для обновления зоны DNS.

### Вариант 1
 
Укажите зону с помощью имени и группы ресурсов.

	PS C:\> Set-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Вариант 2
Укажите зону с помощью объекта $zone, полученного из командлета Get-AzureDnsZone:

	PS C:\> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> <..modify $zone.Tags here...>
	PS C:\> Set-AzureDnsZone -Zone $zone [-Overwrite]

При использовании команды Set-AzureDnsZone с объектом $zone используются проверки Etag, чтобы параллельные изменения не удалялись. Для подавления этих проверок укажите флажок "-Overwrite". Подробные сведения см. в разделе [Etag и теги](dns-getstarted-create-dnszone.md#Etags-and-tags).

## Удаление зоны DNS

Зоны DNS записей можно удалить с помощью командлета Remove-AzureDnsZone.
 
Прежде чем удалить зону DNS в Azure DNS, необходимо удалить все наборы записей, за исключением записей NS и SOA в корне зоны, которые были созданы автоматически вместе с зоной.

Используйте один из следующих двух способов для удаления зоны DNS.

### Вариант 1

Укажите зону с помощью имени зоны и имени группы ресурсов:

	PS C:\> Remove-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

Для этой операции можно использовать необязательный параметр "-Force", который подавляет запрос подтверждения на удаление зоны DNS.
### Вариант 2

Укажите зону с помощью объекта $zone, полученного из командлета Get-AzureDnsZone:

	PS C:\> $zone = Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureDnsZone -Zone $zone [-Force] [-Overwrite]

Параметр "-Force" имеет такой же эффект, что и в 1 варианте.

Как и для командлета Set-AzureDnsZone, при указании набора записей с помощью объекта $zone вы включаете проверки etag, чтобы убедиться, что параллельные изменения не удаляются. <BR> Необязательный флажок "-Overwrite" подавляет эти проверки. Подробные сведения см. в разделе [Etag и теги](dns-getstarted-create-dnszone.md#Etags-and-tags).

Объект зоны также можно направить в конвейер, а не передавать в качестве параметра:

	PS C:\> Get-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureDnsZone [-Force] [-Overwrite]

## Дальнейшие действия


[Управление зонами DNS](dns-operations-recordsets.md)

[Автоматизация операций с помощью пакета SDK для .NET](dns-sdk.md)

<!---HONumber=Nov15_HO4-->