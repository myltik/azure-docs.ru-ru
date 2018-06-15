---
title: 'Сценарий: запуск приложений логики с помощью Функций Azure и служебной шины Azure | Документация Майкрософт'
description: Создайте функцию для запуска приложения логики с помощью Функций Azure и служебной шины Azure.
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 89fcd88643bd793935e7476ef32641ffa5ff4713
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299799"
---
# <a name="scenario-trigger-a-logic-app-with-azure-functions-and-azure-service-bus"></a>Сценарий: запуск приложения логики с помощью Функций Azure и служебной шины Azure

Можно использовать функции Azure для создания триггера приложения логики, когда требуется развернуть долго выполняющиеся прослушиватели или задачи. Например, можно создать функцию, которая ожидает передачи данных из очереди и немедленно запускает извещающим триггером приложение логики.

## <a name="build-the-logic-app"></a>Создание приложения логики
В этом примере для каждого приложения логики, которое нужно запускать, создается отдельная функция. Сначала создайте приложение логики с триггером HTTP-запроса. Функция Azure вызывает эту конечную точку при получении каждого сообщения из очереди.  

1. Создайте приложение логики.
2. Выберите триггер **Вручную — При получении HTTP-запроса**.
   При желании можно указать схему JSON, которая будет использоваться для очереди сообщений. Для этого примените специальный инструмент, например [jsonschema.net](http://jsonschema.net). Вставьте полученную схему в триггер. Схемы помогают конструктору понять, какую форму будут иметь данные, чтобы правильней использовать свойства в рабочем процессе.
2. Добавьте дополнительные шаги, которые необходимо выполнить после получения сообщения из очереди. Например, можно отправить сообщение электронной почты с помощью Office 365.  
3. Сохраните приложение логики, чтобы создать URL-адрес обратного вызова для этого триггера приложения логики. URL-адрес отобразится на карточке триггера.

![URL-адрес обратного вызова отображается на карточке триггера][1]

## <a name="build-the-function"></a>Создание функции
Далее следует создать функцию, которая выступает в качестве триггера и ожидает передачи данных из очереди.

1. На [портале функций Azure](https://functions.azure.com/signin) выберите команду **New Function** (Создать функцию), а затем выберите шаблон **ServiceBusQueueTrigger - C#**.
   
    ![портале функций Azure][2]
2. Настройте подключение к очереди служебной шины, где используется прослушиватель `OnMessageReceive()` из пакета SDK для служебной шины Azure.
3. Напишите простую функцию для вызова конечной точки приложения логики (созданной ранее), используя сообщение очереди в качестве триггера. Ниже приводится полноценный пример такой функции. В примере используется сообщение с типом контента `application/json`, но этот тип при необходимости можно изменить.
   
   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
   
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Для проверки работы можно поместить сообщение в очередь с помощью инструмента [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). Приложение логики должно запуститься сразу же, как только функция получит это сообщение.

<!-- Image References -->
[1]: ./media/logic-apps-scenario-function-sb-trigger/manualtrigger.png
[2]: ./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png
