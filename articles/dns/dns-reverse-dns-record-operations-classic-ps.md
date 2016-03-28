<properties 
   pageTitle="Как управлять обратными записями DNS служб с помощью PowerShell в классической модели развертывания | Microsoft Azure"
   description="Как управлять обратными записями DNS или записями типа PTR для служб Azure с помощью PowerShell в классической модели развертывания."
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# Как управлять обратными записями DNS для служб (классическая модель) с помощью PowerShell

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](dns-reverse-dns-record-operations-ps.md).

## Проверка обратных записей DNS
Чтобы гарантировать, что третья сторона не сможет создать сопоставление обратных записей DNS с вашими доменами DNS, Azure позволяет создавать только обратные записи DNS, для которых выполняется одно из следующих условий:

- Обратное полное доменное имя DNS является именем облачной службы, для которой оно было указано, или именем любой облачной службы в той же подписке, т. е. обратный DNS — contosoapp1.cloudapp.net.
- При переадресации обратное полное доменное имя DNS разрешается в имя или IP-адрес облачной службы, для которой оно было указано, либо имя или IP-адрес любой облачной службы в той же подписке, т. е. обратный DNS — app1.contoso.net, представляющий собой псевдоним CName для contosoapp1.cloudapp.net.

Проверки выполняются только в том случае, если задается или меняется свойство обратного DNS для облачной службы. Периодическая повторная проверка не выполняется.

## Добавление обратного DNS для существующих облачных служб
Вы можете добавить обратную запись DNS для существующей облачной службы, используя командлет Set-AzureService.

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Создание облачной службы с обратным DNS  
Вы можете добавить новую облачную службу со свойством обратного DNS, указанного с помощью командлета Set-AzureService.

	PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Просмотр обратного DNS существующих облачных служб
Вы можете просмотреть настроенное значение существующей облачной службы с помощью командлета Get-AzureService.

	PS C:\> Get-AzureService "contosoapp1"

## Удаление обратного DNS существующих облачных служб
Вы можете удалить свойство обратного DNS из существующей облачной службы с помощью командлета Set-AzureService. Для этого нужно указать пустое значение свойства обратного DNS.

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [ЧАСТО ЗАДАВАЕМЫЕ ВОПРОСЫ](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]

<!----HONumber=AcomDC_0316_2016-->