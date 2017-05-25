---
title: "Примеры и сценарии для Azure Logic Apps | Документация Майкрософт"
description: "Примеры приложений логики для распространенных сценариев."
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: .net,nodejs,java
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/14/2017
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: dcf089d680249d0a2f9d748b315076d91c8c78e8
ms.contentlocale: ru-ru
ms.lasthandoff: 03/30/2017


---
# <a name="examples-and-common-scenarios-for-azure-logic-apps"></a>Примеры и распространенные сценарии для Azure Logic Apps

Чтобы помочь вам узнать больше о множестве шаблонов и возможностей в Azure Logic Apps, ниже приведены распространенные примеры и сценарии.

## <a name="key-scenarios-for-logic-apps"></a>Основные сценарии для приложений логики

Служба Azure Logic Apps обеспечивает устойчивую оркестрацию и интеграцию для различных служб. Logic Apps является службой "без сервера", поэтому не нужно беспокоиться о масштабирования или экземплярах. Все, что нужно сделать, — определить рабочий процесс (триггер и действия). Базовая платформа обеспечивает масштабирование, доступность и производительность. Любой сценарий, в котором необходимо координировать несколько действий, особенно в нескольких системах, замечательно подходит для Azure Logic Apps. Ниже приведено несколько шаблонов и примеров.

## <a name="respond-to-triggers-and-extend-actions"></a>Реагирование на триггеры и расширение действий

Каждое приложение логики начинается с триггера. Например, рабочий процесс можно начать с запланированного события, вызова вручную или события из внешней системы, например триггера "при добавлении файла на FTP-сервер". Служба Azure Logic Apps в настоящее время поддерживает более 100 готовых соединителей для различных решений, от локальной системы SAP до Azure Cognitive Services. Для систем и служб, для которых соединители не опубликованы, можно также расширить приложения логики.

* [Руководство. Создание панели мониторинга социальных сетей на базе ИИ за несколько минут с помощью Logic Apps и Power BI](http://aka.ms/logicappsdemo)
* [Создание настраиваемых триггеров или действий](../logic-apps/logic-apps-create-api-app.md)
* [Настройка длительных действий для запусков рабочего процесса](../logic-apps/logic-apps-create-api-app.md)
* [Реагирование на внешние события и действия с помощью объектов webhook](../logic-apps/logic-apps-create-api-app.md)
* [Вызов, активация или вложение рабочих процессов с использованием синхронных ответов на HTTP-запросы](logic-apps-http-endpoint.md)
* [Руководство. Реагирование на объекты webhook для SMS Twilio и отправка текстового ответа](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-logging-and-control-flow-capabilities"></a>Возможности обработки ошибок, ведения журнала и потока управления

Служба Logic Apps предоставляет широкие возможности для расширенного потока управления, в том числе условия, выключатели, циклы и области. Чтобы обеспечить устойчивость решений, в рабочих процессах можно также реализовать обработку ошибок и исключений. Для ведения журналов уведомлений и диагностики состояния рабочего процесса служба Azure Logic Apps обеспечивает средства мониторинга и уведомления.

* [Выполнение различных действий с помощью операторов switch](logic-apps-switch-case.md)
* [Обработка элементов в массивах и коллекциях с помощью циклов и пакетов в приложениях логики](logic-apps-loops-and-scopes.md)
* [Реализация обработки ошибок и исключений в рабочем процессе](logic-apps-exception-handling.md)
* [Мониторинг приложений логики](logic-apps-monitor-your-logic-apps.md)
* [Вариант использования. Как компания в сфере здравоохранения использует обработку исключений в приложении логики для рабочих процессов HL7 FHIR](logic-apps-scenario-error-and-exception-handling.md)

## <a name="deploy-and-manage-logic-apps"></a>Развертывание приложений логики и управление ими

Можно полностью разработать и развернуть приложения логики с помощью Visual Studio, Visual Studio Team Services или любых других инструментов системы управления версиями и автоматической сборки. Для поддержки развертывания рабочих процессов и зависимых подключений в шаблоне ресурсов приложения логики используют шаблоны развертывания ресурсов Azure. Средства Visual Studio автоматически создают эти шаблоны, которые можно записать после изменения в систему управления версиями для управления версиями.

* [Создание шаблона для автоматического развертывания](../logic-apps/logic-apps-create-deploy-template.md)
* [Развертывание из Visual Studio](logic-apps-deploy-from-vs.md)
* [Мониторинг работоспособности приложений логики](logic-apps-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Типы содержимого и их преобразование во время выполнения

Можно использовать и преобразовывать различные типы содержимого с помощью многих функций, написанных на [языке определения рабочего процесса](http://aka.ms/logicappsdocs) Azure Logic Apps. Например, можно осуществлять взаимное преобразование строк, JSON и XML с помощью выражений рабочего процесса `@json()` и `@xml()`. Обработчик Logic Apps сохраняет типы содержимого, чтобы обеспечить передачу содержимого между службами без потерь.

* [Обработка типов содержимого, отличных от JSON](../logic-apps/logic-apps-content-type.md), например `application/xml`, `application/octet-stream` и `multipart/formdata`
* [Как выражения рабочего процесса работают в приложениях логики](../logic-apps/logic-apps-author-definitions.md)
* [Справочник. Язык определения рабочего процесса Azure Logic Apps](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Прочие возможности интеграции и функции

Служба Logic Apps также поддерживает интеграцию со многими службами, включая Функции Azure, управление API Azure, службы приложений Azure и пользовательские конечные точки HTTP, например REST и SOAP.

* [Создание панели мониторинга сведений о клиентах в режиме реального времени с помощью Функций Azure и Azure Logic Apps](logic-apps-scenario-social-serverless.md)
* [Вызов Функций Azure из приложений логики](../logic-apps/logic-apps-azure-functions.md)
* [Сценарий. Активация приложений логики с помощью Функций Azure](logic-apps-scenario-function-sb-trigger.md)
* [Блог. Вызов конечных точек SOAP из приложения логики](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="next-steps"></a>Дальнейшие действия

- [Обработка ошибок и исключений в приложениях логики](logic-apps-exception-handling.md)
- [Создание определений рабочих процессов на языке определения рабочего процесса](logic-apps-author-definitions.md)
- [Отправка комментариев, вопросов, отзывов или предложений по улучшению Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)
