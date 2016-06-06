<properties
   pageTitle="Сценарии и примеры использования приложений логики | Microsoft Azure"
   description="Примеры распространенных сценариев использования приложений логики, а также способы их реализации."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/25/2016"
   ms.author="deonhe"/>

# Примеры приложений логики и распространенные сценарии

В этом документе описаны распространенные сценарии и примеры с разъяснениями того, как можно использовать приложения логики для автоматизации бизнес-процессов.

## Активация приложения логики

Есть несколько способов активации приложений логики из другого приложения. Вот некоторые из них:

- [HTTP-запрос (POST)](app-service-logic-http-endpoint.md):
- [Получение Webhook](app-service-logic-create-api-app.md)
- [Опрос конечной точки](app-service-logic-create-api-app.md)

### Сценарии

- [Синхронный ответ на запрос приложений логики](app-service-logic-http-endpoint.md)

## Типы содержимого, конверсии и преобразования

[Язык определения рабочего процесса](http://aka.ms/logicappsdocs) для приложений логики содержит множество функций, позволяющих преобразовывать различные типы содержимого и работать с ними. Кроме того, модуль делает все возможное для того, чтобы в ходе рабочего процесса сохранить типы содержимого как потоки данных.

- [Обработка типов содержимого](app-service-logic-content-type.md), таких как application/json, application/xml и plain/text
- [Язык определения рабочего процесса](http://aka.ms/logicappsdocs)

## Продолжительные действия

- [Создание длительного действия](app-service-logic-create-api-app.md)

## Пакеты и циклы

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## Интеграция с вложенными рабочими процессами и функциями Azure

- [Интеграция функций Azure](app-service-logic-azure-functions.md)

## HTTP, REST и SOAP

 - [Вызов SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Мы будем и дальше добавлять сценарии и примеры в этот документ. Сообщите нам в комментариях, какие примеры и сценарии вы хотите увидеть здесь.

<!---HONumber=AcomDC_0525_2016-->