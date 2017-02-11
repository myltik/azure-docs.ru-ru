---
title: "Сценарии и примеры использования приложений логики | Документация Майкрософт"
description: "Примеры распространенных сценариев использования приложений логики, а также способы их реализации."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 4fde1a22ac76306e79a68a37e110828da9a2ef13
ms.openlocfilehash: 3cf2d8b19dd5785317a382dc429feda02d8a2f20


---
# <a name="logic-apps-examples-and-common-scenarios"></a>Примеры приложений логики и распространенные сценарии
В этом документе описаны распространенные сценарии и примеры с разъяснениями того, как можно использовать приложения логики для автоматизации бизнес-процессов. 

## <a name="custom-triggers-and-actions"></a>Пользовательские триггеры и действия
Есть несколько способов активации приложений логики из другого приложения. Вот некоторые распространенные примеры:

* [создание пользовательского триггера или действия](app-service-logic-create-api-app.md)
* [использование продолжительного действия](app-service-logic-create-api-app.md)
* [использование триггера HTTP-запросов (POST)](app-service-logic-http-endpoint.md)
* [использование триггеров и действий](app-service-logic-create-api-app.md)
* [Опрос триггеров](app-service-logic-create-api-app.md)

### <a name="scenarios"></a>Сценарии
* [Приложения логики как вызываемые конечные точки](app-service-logic-http-endpoint.md)
* [Ответ на запрос с помощью SMS](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-and-logging"></a>Обработка ошибок и ведение журнала
* [Обработка ошибок и исключений](app-service-logic-exception-handling.md)
* [Мониторинг приложений логики](app-service-logic-monitor-your-logic-apps.md)

### <a name="scenarios"></a>Сценарии
* [Logging and Error Handling in Logic Apps (Ведение журналов и обработка ошибок в Logic Apps)](app-service-logic-scenario-error-and-exception-handling.md)

## <a name="deploying-and-managing"></a>Развертывание и управление
* [Создание шаблона развертывания приложения логики](app-service-logic-create-deploy-template.md)
* [Развертывание из Visual Studio](app-service-logic-deploy-from-vs.md)
* [Мониторинг приложений логики](app-service-logic-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations"></a>Типы содержимого, конверсии и преобразования
[Язык определения рабочего процесса](http://aka.ms/logicappsdocs) для Logic Apps содержит множество функций для преобразования разных типов содержимого и работы с ними. Кроме того, модуль делает все возможное для того, чтобы в ходе рабочего процесса сохранить типы содержимого как потоки данных.

* [Обработка типов содержимого](app-service-logic-content-type.md), таких как application/json, application/xml и text/plain.
* [Создание определений приложений логики](app-service-logic-author-definitions.md)
* [Workflow definition language reference (Язык определения рабочего процесса)](http://aka.ms/logicappsdocs)

## <a name="batches-and-looping"></a>Пакеты и циклы
* [SplitOn](app-service-logic-loops-and-scopes.md)
* [ForEach](app-service-logic-loops-and-scopes.md)
* [Until](app-service-logic-loops-and-scopes.md)

## <a name="integrating-with-azure-functions"></a>Интеграция с функциями Azure
* [Использование функций Azure с приложениями логики](app-service-logic-azure-functions.md)

### <a name="scenarios"></a>Сценарии
* [Сценарий приложения логики: создание триггера служебной шины Azure с помощью функций Azure](app-service-logic-scenario-function-sb-trigger.md)

## <a name="http-rest-and-soap"></a>HTTP, REST и SOAP
* [Вызов SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

Мы будем и дальше добавлять сценарии и примеры в этот документ. Сообщите нам в комментариях, какие примеры и сценарии вы хотите увидеть здесь.




<!--HONumber=Dec16_HO2-->


