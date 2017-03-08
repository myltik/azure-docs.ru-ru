---
title: "Управление обратными записями DNS для служб Azure с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "Как управлять обратными записями DNS или записями типа PTR для служб Azure с помощью Azure CLI в Resource Manager."
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
ms.date: 02/27/2017
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 1ac7bc232082100cd0f0fe3e337930a6c5e9ede9
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Как управлять обратными записями DNS для служб Azure с помощью Azure CLI

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

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Если вы хотите добавить обратный DNS для существующего общедоступного IP-адреса, у которого еще нет DNS-имени, необходимо также указать DNS-имя. Для этого можно использовать команду azure network public-ip set.

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Создание общедоступного IP-адреса с обратным DNS
Вы можете добавить новый общедоступный IP-адрес с заданным свойством обратного DNS с помощью команды azure network public-ip create.

```azurecli
az network public-ip create --name PublicIp --resource-group NRP-DemoRG-PS --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Просмотр обратного DNS существующих общедоступных IP-адресов
Вы можете просмотреть значение, настроенное для существующего общедоступного IP-адреса, с помощью команды azure network public-ip show.

```azurecli
 az network public-ip show --name PublicIp --resource-group NRP-DemoRG-PS
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Удаление обратного DNS для существующих общедоступных IP-адресов
Вы можете удалить свойство обратного DNS для существующего общедоступного IP-адреса с помощью команды azure network public-ip set. Для этого нужно указать пустое значение свойства ReverseFqdn.

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn ""
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


