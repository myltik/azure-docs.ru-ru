.<properties
 pageTitle="Ограничения и значения по умолчанию планировщика"
 description="Ограничения и значения по умолчанию планировщика"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="krisragh"/>

# Ограничения и значения по умолчанию планировщика

## Квоты планировщика, ограничения, значения по умолчанию и регулирования

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## Заголовок x-ms-идентификатор-запроса

Каждый запрос к службе планировщика возвращает заголовок ответа с именем **x-ms-идентификатор-запроса**. Этот заголовок содержит непрозрачное значение, которое однозначно определяет запрос.

Если запрос постоянно завершается неудачей, но однозначно составлен правильно, используйте это значение, чтобы сообщить об ошибке в корпорацию Майкрософт. Укажите в отчете значение ofx-x-ms-request-id, примерное время создания запроса, идентификатор подписки, коллекцию заданий и (или) задание и тип операции, которую пытался выполнить запрос.

## См. также


 [Что такое планировщик?](scheduler-intro.md)

 [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)

 [Приступая к работе с планировщиком Azure на портале Azure](scheduler-get-started-portal.md)

 [Планы и выставление счетов в планировщике Azure](scheduler-plans-billing.md)

 [Справочник по API REST планировщика Azure](https://msdn.microsoft.com/library/mt629143)

 [Справочник по командлетам PowerShell планировщика Azure](scheduler-powershell-reference.md)

 [Высокая доступность и надежность планировщика Azure](scheduler-high-availability-reliability.md)

 [Исходящая аутентификация планировщика Azure](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0824_2016-->