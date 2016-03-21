<properties 
   pageTitle="Как управлять обратными записями DNS для служб с помощью PowerShell в Resource Manager | Microsoft Azure"
   description="Как управлять обратными записями DNS или записями типа PTR для служб Azure с помощью PowerShell в Resource Manager."
   services="DNS"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/16/2016"
   ms.author="joaoma" />

# Как управлять обратными записями DNS для служб с помощью PowerShell

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]<BR>[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).


## Добавление обратной записи DNS для существующего общедоступного IP-адреса
Вы можете добавить обратный DNS для существующего общедоступного IP-адреса с помощью командлета Set-AzureRmPublicIpAddress.

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip 

## Создание нового общедоступного IP-адреса с обратной записью DNS
Вы можете добавить новый общедоступный IP-адрес с заданным свойством обратного DNS с помощью командлета New-AzureRmPublicIpAddress.

	PS C:\> New-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS -Location WestUS -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
 
## Просмотр обратной записи DNS существующего общедоступного IP-адреса
Вы можете просмотреть значение, настроенное для существующего общедоступного IP-адреса, с помощью командлета Get-AzureRmPublicIpAddress.

	PS C:\> Get-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS 

## Удаление обратной записи DNS существующего общедоступного IP-адреса
Вы можете удалить свойство обратного DNS для существующего общедоступного IP-адреса с помощью командлета Set-AzureRmPublicIpAddress. Для этого нужно указать пустое значение свойства ReverseFqdn.

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings.ReverseFqdn = ""
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip 

## Проверка обратных записей DNS 
Чтобы гарантировать, что третья сторона не сможет создать сопоставление обратных записей DNS с вашими доменами DNS, Azure позволяет создавать только обратные записи DNS, для которых выполняется одно из следующих условий:

- ReverseFqdn совпадает с Fqdn ресурса общедоступного IP-адреса, для которого он был указан, либо с Fqdn любого общедоступного IP-адреса в той же подписке, т. е. ReverseFqdn — contosoapp1.northus.cloudapp.azure.com.

- При переадресации значение ReverseFqdn разрешается в имя или IP-адрес общедоступного IP-адреса, для которого оно было указано, либо в Fqdn или IP-адрес любого общедоступного IP-адреса в той же подписке, т. е. ReverseFqdn — app1.contoso.net, представляющий собой псевдоним CName для contosoapp1.northus.cloudapp.azure.com. Проверки выполняются только в том случае, если задается или меняется свойство обратного DNS для общедоступного IP-адреса. Периодическая повторная проверка не выполняется.

[AZURE.INCLUDE [ЧАСТО ЗАДАВАЕМЫЕ ВОПРОСЫ](../../includes/dns-reverse-dns-record-operations-faq-include.md)]

<!---HONumber=AcomDC_0309_2016-->