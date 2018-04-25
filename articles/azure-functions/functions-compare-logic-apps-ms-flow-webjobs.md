---
title: Сравнение Microsoft Flow, Logic Apps, функций и веб-заданий Azure
description: 'Сравнение облачных служб Майкрософт, которые оптимизированы для выполнения задач интеграции: Microsoft Flow, Logic Apps, функций и веб-заданий Azure.'
services: functions,app-service\logic
documentationcenter: na
author: tdykstra
manager: cfowler
tags: ''
keywords: microsoft flow, поток, logic apps, функции azure, функции, веб-задания azure, веб-задания, обработка событий, динамическое вычисление, независимая от сервера архитектура
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/09/2018
ms.author: tdykstra
ms.custom: mvc
ms.openlocfilehash: 23501eddc8921ed36a9e7d839660455e04ee9381
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="compare-flow-logic-apps-functions-and-webjobs"></a>Сравнение Microsoft Flow, Logic Apps, функций и веб-заданий

В этой статье сравниваются следующие облачные службы Майкрософт:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Функции Azure](https://azure.microsoft.com/services/functions/)
* [веб-задания службы приложений Azure](../app-service/web-sites-create-web-jobs.md)

Все эти службы используются для настройки интеграции и автоматизации бизнес-процессов. Они позволяют определять входные данные, действия, условия и выходные данные. Каждую отдельную службу можно запускать по расписанию или активировать по запросу. Каждая служба имеет уникальные преимущества, но в этой статье описываются их различия.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Сравнение Microsoft Flow и Azure Logic Apps

Microsoft Flow и Azure Logic Apps спроектированы в рамках подхода *designer-first* как службы интеграции, с помощью которых можно создавать последовательности. Обе службы интегрируются с разными корпоративными приложениями и приложениями SaaS. 

Служба Flow создана на основе Logic Apps. Они используют один и тот же конструктор рабочих процессов и [соединители](../connectors/apis-list.md). 

Служба Microsoft Flow помогает офисным сотрудникам самостоятельно выполнять простые операции интеграции (например, процесс утверждения для библиотеки документов SharePoint), не обращаясь к разработчикам или ИТ-специалистам. Однако Logic Apps обеспечивает расширенные интеграции (например, процессы B2B), где нужно выполнять разработку и операции корпоративного класса и применять методы обеспечения безопасности. Как правило, с течением времени бизнес-процессы становятся более сложными. Поэтому на первых этапах можно использовать поток, а затем при необходимости преобразовать его в приложение логики.

Сведения в следующей таблице помогут определить, какую из этих двух служб лучше всего использовать для определенной интеграции.

|  | Поток | приложения логики; |
| --- | --- | --- |
| Пользователи |Офисные сотрудники, бизнес-пользователи, администраторы SharePoint |Профессиональные интеграторы и разработчики, ИТ-специалисты |
| Сценарии |Самообслуживание |Расширенные интеграции |
| Средство разработки |В браузере и мобильном приложении, только пользовательский интерфейс |В браузере и [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md) доступно [представление кода](../logic-apps/logic-apps-author-definitions.md). |
| Управление жизненным циклом приложений (ALM) |Разработка и тестирование в непроизводственных средах, распространение в рабочей среде по готовности. |DevOps: система управления версиями, тестирование, поддержка, автоматизация и управление в [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Административные функции |Управление средами Flow и политиками защиты от потери данных (DLP), отслеживание лицензирования [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Управление группами ресурсов, подключениями, доступом и ведение журнала [https://portal.azure.com](https://portal.azure.com) |
| Безопасность |Журналы аудита безопасности и соответствия требованиям Office 365, защита от потери данных (DLP), [шифрование неактивных](https://wikipedia.org/wiki/Data_at_rest#Encryption) конфиденциальных данных и т. д. |Обеспечение безопасности Azure: [безопасность Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [центр безопасности](https://azure.microsoft.com/services/security-center/), [журналы аудита](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) и многое другое. |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Сравнение служб "Функции Azure" и Azure Logic Apps

Функции и Logic Apps — это службы Azure для использования бессерверных рабочих нагрузок. Функции Azure — это бессерверная служба вычислений, а Azure Logic Apps предоставляет бессерверные рабочие процессы. Обе службы можно использовать для создания сложных *оркестраций*. Оркестрация — это коллекция функций или шагов, называемых *действиями*  в Logic Apps, которые выполняются для реализации сложных задач. Например, для обработки пакета заказов можно запустить параллельное выполнение множества экземпляров функции, дождаться завершения их работы, а затем выполнить функцию, которая вычислит все полученные результаты.

Для Функций Azure оркестрации разрабатываются путем написания кода и использования [расширения устойчивых функции](durable-functions-overview.md) (предварительная версия). Для Logic Apps оркестрации можно создавать с помощью графического пользовательского интерфейса или изменения файлов конфигурации.

Вы можете комбинировать и сопоставлять служба при создании оркестраций, вызывая функции из приложений логики и приложения логики из функций. Выберите способ создания оркестраций с учетом ваших предпочтений и возможностей, предоставляемых каждой службой. В следующей таблице представлены некоторые основные различия между этими службами:
 
|  | Устойчивые функции | приложения логики; |
| --- | --- | --- |
| Разработка | Code-first (императивный подход) | Designer-first (декларативный подход) |
| Соединение | [Около десяти встроенных типов привязки](functions-triggers-bindings.md#supported-bindings); написание кода для пользовательских привязок | [Большая коллекция соединителей](../connectors/apis-list.md), [пакет интеграции Enterprise для сценариев B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [создание пользовательских соединителей](../logic-apps/custom-connector-overview.md) |
| Действия | Каждое действие является функцией Azure; написание кода для функций действий |[Большая коллекция готовых действий](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Мониторинг | [Azure Application Insights](../application-insights/app-insights-overview.md) | [Портал Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Operations Management Suite](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md), [Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| управления | [REST API](durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Портал Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/en-us/rest/api/logic/), [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.logicapp/?view=azurermps-5.6.0), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Контекст выполнения | Можно запускать [локально](functions-runtime-overview.md) или в облаке | Можно запускать только в облаке|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Сравнение функций и веб-заданий

Подобно решению "Функции Azure", компонент "Веб-задания" службы приложений Azure с пакетом SDK для веб-заданий является службой интеграции на основе модели *code-first*, предназначенной для разработчиков. Обе службы созданы на основе [службы приложений Azure](../app-service/app-service-web-overview.md) и поддерживают следующие возможности: [интеграция системы управления версиями](../app-service/app-service-continuous-deployment.md), [проверка подлинности](../app-service/app-service-authentication-overview.md) и [мониторинг с помощью интеграции Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>Веб-задания и пакет SDK для веб-заданий

Компонент службы приложений *Веб-задания* дает возможность запустить код или скрипт в контексте веб-приложения службы приложений. *SDK для веб-заданий* — это предназначенная для веб-заданий платформа, которая упрощает написание кода для реагирования на события в службах Azure. Например, вы можете отреагировать на создание большого двоичного объекта образа в службе хранилища Azure, создав эскиз. Пакет SDK для веб-заданий выполняется как консольное приложение .NET, которое можно развернуть в веб-задании. 

Веб-задания и пакет SDK для веб-заданий лучше всего использовать вместе, но можно и по отдельности. Веб-задания могут выполнять любые программы или скрипты, выполняемые в песочнице службы приложений. Консольное приложение пакета SDK для веб-заданий можно запускать там же, где и остальные консольные приложения, например на локальных серверах.

### <a name="comparison-table"></a>Сравнительная таблица

Решение "Функции Azure" создано на основе пакета SDK для веб-заданий, поэтому оно использует много тех же триггеров событий и соединений, что и другие службы Azure. Ниже приведены некоторые факторы, которые следует учесть при выборе между решением "Функции Azure" и компонентом "Веб-задания" с пакетом SDK для веб-заданий.

|  | Functions | Компонент "Веб-задания" с пакетом SDK для веб-заданий |
| --- | --- | --- |
|[Бессерверная модель приложения](https://azure.microsoft.com/overview/serverless-computing/) с [автоматическим масштабированием](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Разработка и тестирование в браузере](functions-create-first-azure-function.md) |✔||
|[Оплата по мере пользования](functions-scale.md#consumption-plan)|✔||
|[Интеграция с Logic Apps](functions-twitter-email.md)|✔||
| События триггера |[Таймер](functions-bindings-timer.md)<br>[Очереди и большие двоичные объекты службы хранилища Azure](functions-bindings-storage-blob.md)<br>[Очереди и разделы служебной шины Azure](functions-bindings-service-bus.md)<br>[База данных Azure Cosmos](functions-bindings-cosmosdb.md)<br>[Концентраторы событий Azure](functions-bindings-event-hubs.md)<br>[HTTP или веб-перехватчик (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Сетка событий Azure](functions-bindings-event-grid.md)|[Таймер](functions-bindings-timer.md)<br>[Очереди и большие двоичные объекты службы хранилища Azure](functions-bindings-storage-blob.md)<br>[Очереди и разделы служебной шины Azure](functions-bindings-service-bus.md)<br>[База данных Azure Cosmos](functions-bindings-cosmosdb.md)<br>[Концентраторы событий Azure](functions-bindings-event-hubs.md)<br>[Файловая система](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Поддерживаемые языки  |C#<br>F#<br>JavaScript<br>Java (предварительная версия) |C#<sup>1</sup>|
|Диспетчеры пакетов|NPM и NuGet|NuGet<sup>2</sup>|

<sup>1</sup> Компонент "Веб-задания" (без пакета SDK для веб-заданий) поддерживает C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python и т. д. Это не полный список. Веб-задания могут выполнять любые программы или скрипты, выполняемые в песочнице службы приложений.

<sup>2</sup> Компонент "Веб-задания" (без пакета SDK для веб-заданий) поддерживает NPM и NuGet.

### <a name="summary"></a>Сводка

Функции Azure обеспечивают большую продуктивность разработки, а также дополнительные варианты языков, сред разработки, а также расширенные возможности интеграции со службами Azure и ценообразования. В большинстве случаев они являются оптимальным вариантом.

Ниже приведены два сценария, для которых компонент "Веб-задания" может быть лучшим вариантом.

* Требуется больший контроль над кодом, который прослушивает события, — над объектом `JobHost`. Функции предоставляют ограниченное количество методов настройки поведения `JobHost` в файле [host.json](functions-host-json.md). Иногда необходимо выполнять действия, которые невозможно указать с помощью строки в файле JSON. Например, только пакет SDK для веб-заданий позволяет настроить пользовательскую политику повтора для службы хранилища Azure.
* У вас есть служба приложений, для которой необходимо выполнить фрагменты кода и управлять ими совместно в среде разработки и операций.

Для других сценариев, в которых необходимо выполнять фрагменты кода для интеграции со службами Azure или сторонними службами, выберите решение "Функции Azure", а не компонент "Веб-задания" с пакетом SDK для веб-заданий.

<a name="together"></a>

## <a name="flow-logic-apps-functions-and-webjobs-together"></a>Использование Microsoft Flow, Logic Apps, решений "Функции" и "Веб-задания" вместе

Нет необходимости выбирать только одну из этих служб. Они интегрируются друг с другом так же, как и с внешними службами.

Поток может вызвать приложение логики. Приложение логики и функция могут вызывать друг друга. Пример см. в статье [Создание функции, интегрируемой с Azure Logic Apps](functions-twitter-email.md).

Интеграция между Flow, Logic Apps и функциями со временем улучшается. Вы можете создать что-нибудь в одной службе, а использовать в других.

## <a name="next-steps"></a>Дополнительная информация

Начните работу с создания потока, приложения логики или приложения-функции. Щелкните любую из следующих ссылок:

* [Начало работы с Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Создайте приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Создание первой функции Azure](functions-create-first-azure-function.md)

Дополнительные сведения об этих службах интеграции см. в следующих источниках:

* [Leveraging Azure Functions & Azure App Service for integration scenarios by Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Использование функций Azure и службы приложений Azure в сценариях интеграции. Автор: Кристофер Андерсон (Christopher Anderson))
* [Integrations Made Simple by Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Упрощенные возможности интеграции. Автор: Чарльз Ламанна (Charles Lamanna))
* [Logic Apps Live Webcast](http://aka.ms/logicappslive)
* [Часто задаваемые вопросы о Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
