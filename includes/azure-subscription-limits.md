---
title: включение файла
description: включение файла
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 8f104ab046fad8dfd9086525b8f942f3d2941821
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| --- | --- | --- |
| Ядер на [подписку](../articles/billing-buy-sign-up-azure-subscription.md) <sup>1</sup> |20 |10 000 |
| [Дополнительные администраторы](../articles/billing-add-change-azure-subscription-administrator.md) на подписку |200 |200 |
| [Учетные записи хранения](../articles/storage/common/storage-create-storage-account.md) на регион на подписку<sup>2</sup> |200 |250 |
| [Облачные службы](../articles/cloud-services/cloud-services-choose-me.md) на подписку |20 |200 |
| [Локальные сети](http://msdn.microsoft.com/library/jj157100.aspx) на подписку |10 |500 |
| Серверов баз данных SQL на подписку |6 |200 |
| Серверы DNS на подписку |9 |100 |
| Зарезервированные IP на подписку |20 |100 |
| Сертификаты размещенной службы на подписку |199 |199 |
| [Территориальные группы](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) на подписку |256 |256 |


<sup>1</sup>Сверхмалые экземпляры считаются как одно ядро в отношении ограничения ядер, несмотря на частичную загрузку ядра.

<sup>2</sup>Ограничение учетной записи хранения распространяются на учетные записи хранения класса "Стандартный" и "Премиум". Если требуется более 200 учетных записей хранения в одном регионе, обратитесь с запросом в [службу поддержки Azure](https://azure.microsoft.com/support/faq/). Команда хранилища Azure рассмотрит вашу бизнес-модель и может утвердить до 250 учетных записей хранения. 

