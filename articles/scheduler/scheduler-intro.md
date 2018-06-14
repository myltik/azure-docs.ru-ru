---
title: Что такое планировщик Azure? | Документация Майкрософт
description: Планировщик Azure позволяет описывать действия, которые выполняются в облаке. Затем он планирует и выполняет эти действия автоматически.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: a3bf1aacd6978499d7ef77cbcb451a06b857ac38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "22715120"
---
# <a name="what-is-azure-scheduler"></a>Что такое планировщик Azure?
Планировщик Azure позволяет описывать действия, которые выполняются в облаке. Затем он планирует и выполняет эти действия автоматически.  Планировщик делает это с помощью [портала Azure](scheduler-get-started-portal.md), кода, [REST API](https://msdn.microsoft.com/library/mt629143.aspx) или Azure PowerShell.

Планировщик создает, обслуживает и вызывает запланированную работу.  Планировщик не размещает какую-либо рабочую нагрузку и не выполняет код. Он только *вызывает* код, размещенный в другом месте — в Azure, на локальном компьютере или у другого поставщика. Он выполняет вызов с помощью HTTP, HTTPS, очереди хранилища, очереди сервисной шины или раздела сервисной шины.

Планировщик планирует [задания](scheduler-concepts-terms.md), ведет журнал результатов выполнения заданий, который можно просмотреть, а также детерминированно и безотказно планирует рабочие нагрузки. Веб-задания Azure (часть компонента веб-приложений в службе приложений Azure) и другие функции планирования Azure используют планировщик в фоновом режиме. [REST API планировщика](https://msdn.microsoft.com/library/mt629143.aspx) помогает управлять обменом данными для этих действий. Таким образом, планировщик поддерживает [сложные расписания и расширенное повторение](scheduler-advanced-complexity.md) .

Существует несколько вариантов использования планировщика. Например: 

* *Повторение действий приложения.* Периодический сбор данных из Twitter и их передача в веб-канал.
* *Ежедневное обслуживание.* Ежедневное удаление журналов, создание резервных копий и другие задачи обслуживания. Например, администратор может запланировать резервное копирование базы данных ежедневно в 1:00 в течение следующих девяти месяцев.

Планировщик позволяет создавать, обновлять, удалять, просматривать [коллекции заданий](scheduler-concepts-terms.md) , а также управлять ими программным способом (с помощью сценариев) или на портале.

## <a name="see-also"></a>См. также
 [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)

 [Приступая к работе с планировщиком Azure на портале Azure](scheduler-get-started-portal.md)

 [Планы и выставление счетов в планировщике Azure](scheduler-plans-billing.md)

 [Как создавать сложные расписания и расширенное повторение с помощью планировщика Azure](scheduler-advanced-complexity.md)

 [Справочник по API REST планировщика Azure](https://msdn.microsoft.com/library/mt629143)

 [Справочник по командлетам PowerShell планировщика Azure](scheduler-powershell-reference.md)

 [Высокая доступность и надежность планировщика Azure](scheduler-high-availability-reliability.md)

 [Ограничения, значения по умолчанию и коды ошибок планировщика Azure](scheduler-limits-defaults-errors.md)

 [Исходящая аутентификация планировщика Azure](scheduler-outbound-authentication.md)

