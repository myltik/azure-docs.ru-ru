---
title: Приступая к работе с планировщиком Azure на портале Azure | Документация Майкрософт
description: Начало работы с планировщиком Azure на портале Azure
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: deli
ms.openlocfilehash: f03ddb475835b30e9b931b7f057c062b57ac45f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31413436"
---
# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Начало работы с планировщиком Azure на портале Azure
Запланированные задания можно легко создавать в планировщике Azure. Из этого руководства вы узнаете, как создать такое задание. Также вы изучите возможности мониторинга и управления в планировщике.

## <a name="create-a-job"></a>создать задание;
1. Войдите на [портал Azure](https://portal.azure.com/).  
2. Щелкните **+Создать**, введите в поле поиска *Планировщик*, в результатах выберите **Планировщик**, а затем щелкните **Создать**.
   
    ![][marketplace-create]
3. Создадим задание, которое будет только открывать сайт http://www.microsoft.com/ с помощью запроса GET. На экране **Задание планировщика** введите следующие сведения:
   
   1. **Имя:** `getmicrosoft`.  
   2. **Подписка:** ваша подписка Azure.   
   3. **Коллекция заданий:** выберите существующую коллекцию заданий или щелкните **Создать** и введите имя.
4. Затем на вкладке **Параметры действия**определите следующие значения:
   
   1. **Тип действия:** ` HTTP`.  
   2. **Метод:** `GET`.  
   3. **URL-адрес:** ` http://www.microsoft.com`.  
      
      ![][action-settings]
5. И, наконец, определим расписание. Задание можно определить как разовое, но в данном случае мы выберем расписание повторения.
   
   1. **Периодичность:** `Recurring`.
   2. **Начало**: текущая дата.
   3. **Повторять каждые:** `12 Hours`.
   4. **Окончание**: через два дня после текущей даты.  
      
      ![][recurrence-schedule]
6. Нажмите кнопку **Создать**

## <a name="manage-and-monitor-jobs"></a>Контроль и мониторинг заданий
Созданное задание появляется на основной панели мониторинга Azure. Если щелкнуть его, откроется новое окно со следующими вкладками.

1. properties  
2. Параметры действия  
3. Расписание  
4. Журнал
5. Пользователи
   
   ![][job-overview]

### <a name="properties"></a>properties
Эти свойства, предназначенные только для чтения, содержат метаданные управления для задания планировщика.

   ![][job-properties]

### <a name="action-settings"></a>Параметры действия
Чтобы изменить параметры задания, выберите его на экране **Задания** . Вы можете настроить дополнительные параметры, если вы не настроили их в мастере быстрого создания.

Для всех типов действий можно изменить политику повторов и действие при возникновении ошибки.

Типы заданий HTTP и HTTPS позволяют изменить метод для любого допустимого глагола HTTP. Кроме того, можно добавлять, удалять или изменять заголовки и данные обычной проверки подлинности.

Типы заданий очереди хранения позволяют изменить учетную запись хранения, имя очереди, маркер SAS и текст.

Для типов действий служебной шины можно изменить пространство имен, путь к разделу или очереди, параметры проверки подлинности, тип транспорта, свойства сообщения и текст сообщения.

   ![][job-action-settings]

### <a name="schedule"></a>Расписание
На этой вкладке можно изменить настройки расписания, созданного в мастере быстрого создания.

Здесь можно [создавать сложные расписания и расширенное повторение в задании](scheduler-advanced-complexity.md)

Можно изменить дату и времени начала, расписание повторения, а также дату и время окончания (если задание повторяется.)

   ![][job-schedule]

### <a name="history"></a>Журнал
На вкладке **Журнал** отображаются выбранные метрики для каждого процесса выполнения выбранного задания в системе. Эти метрики содержат обновляемые в реальном времени значения, которые отражают работоспособность планировщика.

1. Status  
2. Сведения  
3. Количество повторных попыток.
4. Периодичность: 1, 2, 3 и т. д.
5. Время начала выполнения.  
6. Время окончания выполнения.
   
   ![][job-history]

Щелкните один из процессов выполнения, чтобы просмотреть **информацию журнала**, в том числе полный ответ для каждого выполнения. В этом диалоговом окне также можно скопировать ответ в буфер обмена.

   ![][job-history-details]

### <a name="users"></a>Пользователи
Управление доступом на основе ролей (RBAC) Azure обеспечивает точное управление доступом для планировщика Azure. Сведения о том, как использовать вкладку "Пользователи", см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="see-also"></a>См. также
 [Что такое планировщик?](scheduler-intro.md)

 [Основные понятия, терминология и иерархия сущностей планировщика](scheduler-concepts-terms.md)

 [Планы и выставление счетов в планировщике Azure](scheduler-plans-billing.md)

 [Как создавать сложные расписания и расширенное повторение с помощью планировщика Azure](scheduler-advanced-complexity.md)

 [Справочник по REST API планировщика](https://msdn.microsoft.com/library/mt629143)

 [Справочник по командлетам PowerShell планировщика](scheduler-powershell-reference.md)

 [Высокая доступность и надежность планировщика](scheduler-high-availability-reliability.md)

 [Ограничения и значения по умолчанию планировщика](scheduler-limits-defaults-errors.md)

 [Исходящая аутентификация планировщика](scheduler-outbound-authentication.md)

[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
