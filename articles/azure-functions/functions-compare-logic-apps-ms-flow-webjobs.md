---
title: "Сравнение Microsoft Flow, Logic Apps, Функций и веб-заданий | Документация Майкрософт"
description: "Эта статья позволяет сравнить облачные службы интеграции Microsoft и выбрать наиболее оптимальный для вас вариант."
services: functions,app-service\logic
documentationcenter: na
author: cephalin
manager: wpickett
tags: 
keywords: "microsoft flow, поток, logic apps, функции azure, функции, веб-задания azure, веб-задания, обработка событий, динамическое вычисление, независимая от сервера архитектура"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/08/2016
ms.author: chrande; glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0f35d79870301bcba9f009f7dbc00696ce426458
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Сравнение Microsoft Flow, Logic Apps, функций и веб-заданий Azure
В этой статье сравниваются следующие службы Microsoft Cloud, используемые для настройки интеграции и автоматизации бизнес-процессов:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Функции Azure](https://azure.microsoft.com/services/functions/)
* [веб-задания службы приложений Azure](../app-service-web/web-sites-create-web-jobs.md)

Все эти службы особенно полезны при объединении разрозненных систем. Они позволяют определять входные данные, действия, условия и выходные данные. Каждую отдельную службу можно запускать по расписанию или активировать по запросу. Каждая из них добавляет уникальный набор значений. Поэтому цель сравнения служб — определить не лучшую из них, а самую подходящую в определенном сценарии. Обычно, чтобы быстро создать масштабируемое полнофункциональное решение интеграции, эти службы необходимо использовать вместе.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Сравнение Flow Приложения логики
Службы Microsoft Flow и Azure Logic Apps — это службы интеграции на основе модели *configuration-first*, которые упрощают создание процессов и рабочих процессов, а также обеспечивают интеграцию с разными корпоративными приложениями и приложениями SaaS. Поэтому они рассматриваются вместе. 

* Служба Flow создана на основе Logic Apps.
* У них один и тот же конструктор рабочих процессов.
* [соединители](../connectors/apis-list.md) .

Служба Microsoft Flow помогает офисным сотрудникам самостоятельно выполнять простые интеграции (например, настроить отправку SMS-сообщений при получении важных сообщений электронной почты), не обращаясь к обработчикам или ИТ-специалистам. Однако Logic Apps обеспечивает более широкие и критически важные интеграции (например, процессы B2B), где нужно выполнять разработку и операции корпоративного класса и применять методы безопасности. Как правило, с течением времени бизнес-процессы становятся более сложными. Поэтому на первых этапах можно использовать поток, а затем при необходимости преобразовать его в приложение логики.

Сведения в следующей таблице помогут определить, какую из этих двух служб лучше всего использовать для определенной интеграции.

|  | Поток | Приложения логики |
| --- | --- | --- |
| Аудитория |Офисные сотрудники, бизнес-пользователи |ИТ-специалисты, разработчики |
| Сценарии |Самообслуживание |Критически важные интеграции |
| Средство разработки |В браузере, только пользовательский интерфейс |В браузере и [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md) доступно [представление кода](../logic-apps/logic-apps-author-definitions.md). |
| Разработка и операции |Ad-hoc, разработка в рабочей среде |Система управления версиями, тестирование, поддержка, автоматизация и управление в [Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md) |
| Административные функции |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| Безопасность |Стандартные методы безопасности: [независимость данных](https://wikipedia.org/wiki/Technological_Sovereignty), [шифрование при хранении](https://wikipedia.org/wiki/Data_at_rest#Encryption) для конфиденциальных данных и т. д. |Обеспечение безопасности Azure: [безопасность Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [центр безопасности](https://azure.microsoft.com/services/security-center/), [журналы аудита](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) и многое другое. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Сравнение функций и Веб-задания
Функции Azure и веб-задания службы приложений Azure рассматриваются вместе, так как они являются службами интеграции на основе модели *code-first* и предназначены для разработчиков. Они позволяют выполнять сценарии или фрагменты кода в ответ на разные события, например [создание больших двоичных объектов хранилища](functions-bindings-storage.md) или отправку [запроса webhook](functions-bindings-http-webhook.md). Сходства между функциями Azure и веб-заданиями службы приложений Azure: 

* Созданы на основе [службы приложений Azure](../app-service/app-service-value-prop-what-is.md) и поддерживают одинаковые возможности, такие как [система управления версиями](../app-service-web/app-service-continuous-deployment.md), [аутентификация](../app-service/app-service-authentication-overview.md) и [мониторинг](../app-service-web/web-sites-monitor.md).
* Предназначены для разработчиков.
* Поддерживают стандартные языки программирования и скриптов.
* Поддерживают NuGet и NPM.

Функции были созданы на основе веб-заданий. В них сочетаются все лучшие возможности веб-заданий, а также добавлены некоторые улучшения, среди которых: 

* Упрощенная разработка, тестирование и выполнение кода непосредственно в браузере.
* Встроенная интеграция с большим количеством служб Azure и сторонних служб, таких как [Объекты Webhook GitHub](https://developer.github.com/webhooks/creating/).
* Оплата за использование, не требуется платить за [план службы приложений](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
* Автоматизация, [динамическое масштабирование](functions-scale.md).
* Текущие клиенты службы приложений все еще могут использовать план службы приложений (чтобы воспользоваться малоиспользуемыми ресурсами).
* Интеграция с Logic Apps.

В следующей таблице приведены различия между функциями и веб-заданиями.

|  | Functions | Веб-задания |
| --- | --- | --- |
| Масштабирование |Масштабирование без настройки |Масштабирование в рамках плана службы приложений |
| Цены |Оплата за использование или в рамках плана службы приложений |В рамках плана службы приложений |
| Тип запуска |Активация, по расписанию (с помощью триггера таймера) |Активация, непрерывная работа, по расписанию |
| События триггера |[Таймер](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-documentdb.md), [концентраторы событий Azure](functions-bindings-event-hubs.md), [HTTP или Webhook (GitHub, Slack)](functions-bindings-http-webhook.md), [мобильные приложения службы приложений Azure](functions-bindings-mobile-apps.md), [центры уведомлений Azure](functions-bindings-notification-hubs.md), [служебная шина Azure](functions-bindings-service-bus.md), [служба хранилища Azure](functions-bindings-storage.md) |[Служба хранилища Azure](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [служебная шина Azure](../app-service-web/websites-dotnet-webjobs-sdk-service-bus.md) |
| Разработка в браузере |x | |
| Написание скриптов в окне |Экспериментальная возможность |x |
| PowerShell |Экспериментальная возможность |x |
| C# |x |x |
| F# |x | |
| Bash |Экспериментальная возможность |x |
| PHP |Экспериментальная возможность |x |
| Python |Экспериментальная возможность |x |
| JavaScript |x |x |

Выбор между функциями и веб-заданиями зависит от того, какие процессы уже выполнялись в службе приложений. Если для приложения службы приложений необходимо выполнить фрагменты кода и управлять ими совместно в среде разработки и операций, следует использовать веб-задания. Если вы хотите выполнить фрагменты кода для других служб Azure или сторонних приложений, управлять фрагментами кода для интеграции отдельно от других приложений службы приложений или вызвать фрагменты кода из Logic Apps, то воспользуйтесь преимуществами всех улучшений функций.  

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow, Logic Apps и функции
Как уже отмечалось ранее, выбор конкретной службы зависит от определенной ситуации. 

* Для оптимизации простых бизнес-процессов используйте Flow.
* Если сценарий интеграции слишком сложный для Flow или требуются возможности разработки и операций и обеспечения соответствия требованиям безопасности, то используйте Logic Apps.
* Если в рамках сценария интеграции требуется выполнить значительные преобразования или написать специализированный код, нужно создать приложение-функцию, а затем активировать функцию в качестве действия.

Приложение логики можно вызвать в потоке. Функцию также можно вызвать в приложении логики, а приложение логики — в функции. Интеграция между Flow, Logic Apps и функциями со временем улучшается. Вы можете создать что-нибудь в одной службе, а использовать в других. Поэтому все вложенные в эти три технологии средства оправданы.

## <a name="next-steps"></a>Дальнейшие действия
Начните работу с каждой из этих служб, создав первый поток, приложение логики, приложение-функцию или веб-задание. Щелкните любую из следующих ссылок:

* [Начало работы с Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Создайте приложение логики](../logic-apps/logic-apps-create-a-logic-app.md)
* [Создание первой функции Azure](functions-create-first-azure-function.md)
* [Развертывание веб-заданий с помощью Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

Дополнительные сведения об этих службах интеграции см. в следующих источниках:

* [Leveraging Azure Functions & Azure App Service for integration scenarios by Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Использование функций Azure и службы приложений Azure в сценариях интеграции. Автор: Кристофер Андерсон (Christopher Anderson))
* [Integrations Made Simple by Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Упрощенные возможности интеграции. Автор: Чарльз Ламанна (Charles Lamanna))
* [Logic Apps Live Webcast](http://aka.ms/logicappslive)
* [Часто задаваемые вопросы о Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
* [Документация по веб-заданиям Azure](../app-service-web/websites-webjobs-resources.md)


