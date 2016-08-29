.<properties
	pageTitle="Список задач администрирования и разработки в службах BizTalk | Microsoft Azure" 
	description="Планирование и инструкции по развертыванию служб BizTalk Azure"
	services="biztalk-services"
	documentationCenter=""
	authors="msftman"
	manager="erikre"
	editor=""/>

.<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="deonhe"/>

# Список задач администрирования и разработки в службах BizTalk  

## Приступая к работе
При работе со службами BizTalk Microsoft Azure следует принимать во внимание несколько локальных и облачных компонентов. Для начала рассмотрим следующую схему.

|Шаг|Ответственное лицо|Задача|Связанные ссылки|
|----|----|----|----|
1\.|Администратор|Создание подписки Microsoft Azure с помощью учетной записи Майкрософт или учетной записи организации|[Классический портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2)|Администратор|Создание или подготовка службы BizTalk.|[Создание службы BizTalk с помощью классического портала Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3\.|Администратор|Регистрация развертывания службы BizTalk, принадлежащего вам или вашей компании.|[Регистрация и обновление развертывания службы BizTalk на портале служб BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4\.|Администратор|Применяется, если приложение использует службу адаптера BizTalk для подключения к локальной бизнес-системе или использует в качестве места назначения очередь или раздел. Создание пространства имен служебной шины Azure. Предоставление этого пространства имен, значений имени поставщика служебной шины и ключа поставщика служебной шины разработчику.|[Практическое руководство. Создание или изменение пространства имен службы служебной шины](../service-bus/service-bus-dotnet-get-started-with-queues.md) и [Получение значений имени поставщика и ключа поставщика](biztalk-issuer-name-issuer-key.md)|
|5\.|Developer|Установка пакета SDK и создание проекта службы BizTalk в Visual Studio.|[Установка пакета SDK для служб Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689760.aspx) и [Создание конечных точек расширенного обмена сообщениями в Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6\.|Developer|Развертывание проекта службы BizTalk в службе BizTalk, размещенной в Azure.|[Развертывание и обновление проекта служб BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7\.|Администратор|Применяется, если вы используете EDI. Можно добавлять партнеров и создавать соглашения на портале служб BizTalk Microsoft Azure. При создании соглашения в список его параметров можно добавить мост и (или) преобразования, созданные разработчиком.|[Настройка EDI, AS2 и EDIFACT на портале служб BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8\.|Администратор|Использование классического портала Azure, наблюдение за работоспособностью службы BizTalk, включая мониторинг показателей производительности.|[Службы BizTalk: вкладки «Панель мониторинга», «Монитор» и «Масштаб»](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9\.|Администратор|Использование портала служб BizTalk Microsoft Azure, управление артефактами, используемыми службами BizTalk, и отслеживание сообщений по мере их обработки файлами моста.|[Использование портала служб BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10\.|Администратор|Создание плана архивации для резервного копирования службы BizTalk.|[Непрерывность бизнес-процессов и аварийное восстановление в службах BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## Дальнейшие действия
[Учебники и примеры](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Создание проекта в Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Установка пакета SDK служб BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## Основные понятия
[Создание проекта в Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx) [Обмен сообщениями EDI, AS2 и EDIFACT обмена сообщениями (бизнес-бизнес)](https://msdn.microsoft.com/library/azure/hh689898.aspx)
## Другие ресурсы  
[Добавление конечных точек источника, назначения и обмена сообщениями через мост](https://msdn.microsoft.com/library/azure/hh689877.aspx) [Изучение и создание схем сообщений и преобразований](https://msdn.microsoft.com/library/azure/hh689905.aspx) [Использование службы адаптера BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx) [Службы BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)

<!---HONumber=AcomDC_0817_2016-->