<properties
   pageTitle="Сценарии и примеры использования приложений логики | Microsoft Azure"
   description="Примеры распространенных сценариев использования приложений логики, а также способы их реализации."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/01/2016"
   ms.author="jehollan"/>

# Примеры приложений логики и распространенные сценарии

В этом документе описаны распространенные сценарии и примеры с разъяснениями того, как можно использовать приложения логики для автоматизации бизнес-процессов.

## Триггеры и действия

Есть несколько способов активации приложений логики из другого приложения. Вот некоторые из них:

- [создание пользовательского триггера или действия](app-service-logic-create-api-app.md);
- [использование продолжительного действия](app-service-logic-create-api-app.md);
- [использование триггера HTTP-запросов (POST)](app-service-logic-http-endpoint.md);
- [использование триггеров и действий](app-service-logic-create-api-app.md);
- [Опрос триггеров](app-service-logic-create-api-app.md)

### Сценарии

- [Приложения логики как вызываемые конечные точки](app-service-logic-http-endpoint.md)

## Обработка ошибок и ведение журнала

- [Мониторинг приложений логики](app-service-logic-monitor-your-logic-apps.md)

### Сценарии

- [Logging and Error Handling in Logic Apps](app-service-logic-scenario-error-and-exception-handling.md) (Ведение журналов и обработка ошибок в Logic Apps)

## Развертывание и управление

- [Создание шаблона развертывания приложения логики](app-service-logic-create-deploy-template.md)
- [Развертывание из Visual Studio](app-service-logic-deploy-from-vs.md)
- [Мониторинг приложений логики](app-service-logic-monitor-your-logic-apps.md)

## Типы содержимого, конверсии и преобразования

[Язык определения рабочего процесса](http://aka.ms/logicappsdocs) для Logic Apps содержит множество функций для преобразования разных типов содержимого и работы с ними. Кроме того, модуль делает все возможное для того, чтобы в ходе рабочего процесса сохранить типы содержимого как потоки данных.

- [Обработка типов содержимого в приложениях логики](app-service-logic-content-type.md) (application/json, application/xml, plain/text)
- [Создание определений приложений логики](app-service-logic-author-definitions.md)
- [Workflow definition language reference](http://aka.ms/logicappsdocs) (Язык определения рабочего процесса)

## Пакеты и циклы

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## Интеграция с функциями Azure

- [Использование функций Azure с приложениями логики](app-service-logic-azure-functions.md)

### Сценарии

- [Сценарий приложения логики: создание триггера служебной шины Azure с помощью функций Azure](app-service-logic-scenario-function-sb-trigger.md)

## HTTP, REST и SOAP

 - [Вызов SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Мы будем и дальше добавлять сценарии и примеры в этот документ. Сообщите нам в комментариях, какие примеры и сценарии вы хотите увидеть здесь.

<!---HONumber=AcomDC_0803_2016-->