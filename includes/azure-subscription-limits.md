---
title: включение файла
description: включение файла
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 03/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 99766ca4cc9d77927030f81cff6bb9c009874f89
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| Ядер на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) <sup>1</sup> |20 |10 000 |
| [Дополнительные администраторы](../articles/billing-add-change-azure-subscription-administrator.md) на подписку |200 |200 |
| [Учетные записи хранения](../articles/storage/common/storage-create-storage-account.md) на регион на подписку<sup>2</sup> |200 |250 |
| [Облачные службы](../articles/cloud-services/cloud-services-choose-me.md) на подписку |20 |200 |
| [Локальные сети](http://msdn.microsoft.com/library/jj157100.aspx) на подписку |10 |500 |
| Серверов баз данных SQL на подписку |6 |150 |
| Серверы DNS на подписку |9 |100 |
| Зарезервированные IP на подписку |20 |100 |
| Сертификаты размещенной службы на подписку |199 |199 |
| [Территориальные группы](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) на подписку |256 |256 |


<sup>1</sup>Сверхмалые экземпляры считаются как одно ядро в отношении ограничения ядер, несмотря на частичную загрузку ядра.

<sup>2</sup>Ограничение учетной записи хранения распространяются на учетные записи хранения класса "Стандартный" и "Премиум". Если требуется более 200 учетных записей хранения в одном регионе, обратитесь с запросом в [службу поддержки Azure](https://azure.microsoft.com/support/faq/). Команда хранилища Azure рассмотрит вашу бизнес-модель и может утвердить до 250 учетных записей хранения. 

