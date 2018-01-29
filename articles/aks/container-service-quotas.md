---
title: "Квоты службы контейнеров Azure (AKS) и ее доступность в регионах"
description: "Квоты по умолчанию и доступность в регионах для службы контейнеров Azure."
services: container-service
author: david-stanford
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 01/16/2018
ms.author: dastanfo
ms.openlocfilehash: eddfab874501fd045820d7da3c968d340fdffe93
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
---
# <a name="quotas-and-region-availability-for-azure-container-service-aks"></a>Квоты и доступность в регионах для службы контейнеров Azure (AKS)

Все службы Azure включают несколько стандартных ограничений и квот для ресурсов и функций. В разделах ниже содержится подробная информация об ограничениях ресурсов по умолчанию для нескольких ресурсов службы AKS, а также сведения о доступности службы AKS в регионах Azure.

## <a name="service-quotas-and-limits"></a>Квоты и ограничения службы

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Подготовленная инфраструктура

Все другие ограничения сети, вычислительных ресурсов и хранилища применяются к подготовленной инфраструктуре. Сведения о соответствующих ограничениях см. в статье о [подписке Azure и ограничениях службы](../azure-subscription-service-limits.md).

## <a name="region-availability"></a>Регионы доступности

Предварительная версия службы контейнеров Azure доступна в следующих регионах:
- Восток США
- Западная Европа
- Центральный регион США
- Центральная Канада
- Восточная Канада

## <a name="next-steps"></a>Дополнительная информация

Вы можете повысить лимит нескольких ограничений и квот. Чтобы запросить увеличение квоты одного или нескольких ресурсов, которые поддерживают такую возможность, отправьте [запрос в службу поддержки Azure][azure-support] (в качестве **типа проблемы** укажите "Квота").

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
