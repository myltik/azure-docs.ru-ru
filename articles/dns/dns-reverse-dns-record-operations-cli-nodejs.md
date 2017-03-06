---
title: "Управление обратными записями DNS для служб Azure с помощью Azure CLI 1.0 | Документация Майкрософт"
description: "Как управлять обратными записями DNS или записями типа PTR для служб Azure с помощью Azure CLI 1.0 в Resource Manager"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c655707e-1156-4893-b163-0b228ffd25d2
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 227ede3445ced9cca71a3879afb0bb6c8c5c2482
ms.lasthandoff: 02/27/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli-10"></a>Как управлять обратными записями DNS для служб Azure с помощью Azure CLI 1.0

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

* [Azure CLI 1.0](dns-reverse-dns-record-operations-cli-nodejs.md) — это интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
* [Azure CLI 2.0](dns-reverse-dns-record-operations-cli.md) — это интерфейс командной строки нового поколения для модели развертывания Resource Manager.

## <a name="introduction"></a>Введение

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Дополнительные сведения о классической модели развертывания см. в статье [Как управлять обратными записями DNS для служб Azure (классическая модель) с помощью Azure PowerShell](dns-reverse-dns-record-operations-classic-ps.md).


## <a name="validation-of-reverse-dns-records"></a>Проверка обратных записей DNS
Чтобы гарантировать, что третья сторона не сможет создать сопоставление обратных записей DNS с вашими доменами DNS, Azure позволяет создавать только обратные записи DNS, для которых выполняется одно из следующих условий:

* ReverseFqdn совпадает с Fqdn ресурса общедоступного IP-адреса, для которого он был указан, либо с Fqdn любого общедоступного IP-адреса в той же подписке, т. е. ReverseFqdn — contosoapp1.northus.cloudapp.azure.com.
* При переадресации значение ReverseFqdn разрешается в имя или IP-адрес общедоступного IP-адреса, для которого оно было указано, либо в Fqdn или IP-адрес любого общедоступного IP-адреса в той же подписке, т. е. ReverseFqdn — app1.contoso.com, представляющий собой псевдоним CName для contosoapp1.northus.cloudapp.azure.com.

Проверки выполняются только в том случае, если задается или меняется свойство обратного DNS для общедоступного IP-адреса. Периодическая повторная проверка не выполняется.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Добавление обратного DNS для существующих общедоступных IP-адресов
Вы можете добавить обратный DNS для существующего общедоступного IP-адреса с помощью команды azure network public-ip set.

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Если вы хотите добавить обратный DNS для существующего общедоступного IP-адреса, у которого еще нет DNS-имени, необходимо также указать DNS-имя. Для этого можно использовать команду azure network public-ip set.

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Создание общедоступного IP-адреса с обратным DNS
Вы можете добавить новый общедоступный IP-адрес с заданным свойством обратного DNS с помощью команды azure network public-ip create.

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Просмотр обратного DNS существующих общедоступных IP-адресов
Вы можете просмотреть значение, настроенное для существующего общедоступного IP-адреса, с помощью команды azure network public-ip show.

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Удаление обратного DNS для существующих общедоступных IP-адресов
Вы можете удалить свойство обратного DNS для существующего общедоступного IP-адреса с помощью команды azure network public-ip set. Для этого нужно указать пустое значение свойства ReverseFqdn.

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


