---
title: Ограничения и значения по умолчанию планировщика
description: Ограничения и значения по умолчанию планировщика
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23043169"
---
# <a name="scheduler-limits-and-defaults"></a>Ограничения и значения по умолчанию планировщика
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Квоты планировщика, ограничения, значения по умолчанию и регулирования
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Заголовок x-ms-идентификатор-запроса
Каждый запрос к службе планировщика возвращает заголовок ответа с именем**x-ms-идентификатор-запроса**. Этот заголовок содержит непрозрачное значение, которое однозначно определяет запрос.

Если запрос постоянно завершается неудачей, но однозначно составлен правильно, используйте это значение, чтобы сообщить об ошибке в корпорацию Майкрософт. Укажите в отчете значение ofx-x-ms-request-id, примерное время создания запроса, идентификатор подписки, коллекцию заданий и (или) задание и тип операции, которую пытался выполнить запрос.

## <a name="see-also"></a>См. также
 [Что такое планировщик?](scheduler-intro.md)

 [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)

 [Приступая к работе с планировщиком Azure на портале Azure](scheduler-get-started-portal.md)

 [Планы и выставление счетов в планировщике Azure](scheduler-plans-billing.md)

 [Справочник по API REST планировщика Azure](https://msdn.microsoft.com/library/mt629143)

 [Справочник по командлетам PowerShell планировщика Azure](scheduler-powershell-reference.md)

 [Высокая доступность и надежность планировщика Azure](scheduler-high-availability-reliability.md)

 [Исходящая аутентификация планировщика Azure](scheduler-outbound-authentication.md)

