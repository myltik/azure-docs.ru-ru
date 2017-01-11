---
title: "Управление обратными записями DNS для служб Azure (классическая модель) с помощью PowerShell | Документация Майкрософт"
description: "Как управлять обратными записями DNS или записями типа PTR для служб Azure с помощью PowerShell в классической модели развертывания. "
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9c24d176-6bce-4277-a14c-80fe44a20a87
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fe5c9cfe0edd769635301fdcb40fc1f71d430165


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Как управлять обратными записями DNS для служб Azure (классическая модель) с помощью Azure PowerShell

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Узнайте, как [выполнить эти действия с помощью модели Resource Manager](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Проверка обратных записей DNS
Чтобы гарантировать, что третья сторона не сможет создать сопоставление обратных записей DNS с вашими доменами DNS, Azure позволяет создавать только обратные записи DNS, для которых выполняется одно из следующих условий:

* Обратное полное доменное имя DNS является именем облачной службы, для которой оно было указано, или именем любой облачной службы в той же подписке, т. е. обратный DNS — contosoapp1.cloudapp.net.
* При переадресации обратное полное доменное имя DNS разрешается в имя или IP-адрес облачной службы, для которой оно было указано, либо имя или IP-адрес любой облачной службы в той же подписке, т. е. обратный DNS — app1.contoso.com, представляющий собой псевдоним CName для contosoapp1.cloudapp.net.

Проверки выполняются только в том случае, если задается или меняется свойство обратного DNS для облачной службы. Периодическая повторная проверка не выполняется.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Добавление обратного DNS для существующих облачных служб
Вы можете добавить обратную запись DNS для существующей облачной службы, используя командлет Set-AzureService.

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Создание облачной службы с обратным DNS
Вы можете добавить новую облачную службу со свойством обратного DNS, указанного с помощью командлета Set-AzureService.

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Просмотр обратного DNS существующих облачных служб
Вы можете просмотреть настроенное значение существующей облачной службы с помощью командлета Get-AzureService.

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Удаление обратного DNS существующих облачных служб
Вы можете удалить свойство обратного DNS из существующей облачной службы с помощью командлета Set-AzureService. Для этого нужно указать пустое значение свойства обратного DNS.

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]




<!--HONumber=Dec16_HO2-->


