<properties
   pageTitle="Сценарий работы приложения логики: триггер служебной шины функций Azure | Microsoft Azure"
   description="Узнайте, как использовать функции Azure в качестве триггера служебной шины для приложений логики"
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>
   
# Сценарий работы приложения логики: триггер служебной шины Функций Azure

Вы можете использовать функции Azure в роли триггера для приложения логики, если вам требуются долго выполняющиеся прослушиватели или задачи. Например, вы можете создать функцию Azure, которая будет прослушивать очередь и немедленно запускать извещающим триггером приложение логики.

## Создание приложения логики

В этом примере для каждого приложения логики, которое нужно запускать, будет создана отдельная функция. Сначала необходимо создать приложение логики с триггером HTTP-запроса. Функция Azure будет вызывать эту конечную точку при получении каждого сообщения из очереди.

1. Откройте новое приложение логики и выберите триггер **Вручную — при получении HTTP-запроса**.  
    * При желании можно указать схему JSON, которая будет использоваться для очереди сообщений. Для этого примените специальное средство, например [jsonschema.net](http://jsonschema.net), и вставьте полученную схему в триггер. Это позволит конструктору понять, какую форму будут иметь данные, чтобы правильно использовать свойства в рабочем процессе.
1. Добавьте все шаги, которые необходимо выполнить после получения сообщения из очереди. Например, вы можете отправить сообщение электронной почты с помощью Office 365.  
1. Сохраните приложение логики, чтобы создать URL-адрес обратного вызова для этого триггера приложения логики. URL-адрес будет отображаться на карточке триггера.

![][1]

## Создание функции Azure

Далее следует создать функцию Azure, которая будет выступать в качестве триггера и прослушивать очередь.

1. Откройте [портал функций Azure](https://functions.azure.com/signin) и выберите команду **Создать функцию** с шаблоном **ServiceBusQueueTrigger - C#**.

    ![][2]

2. Настройка подключения к очереди сервисной шины (с использованием прослушивателя `OnMessageReceive()` из пакета SDK сервисной шины)
3. Напишите простую функцию для вызова конечной точки приложения логики (которую мы рассмотрели выше) для сообщения из очереди. Ниже приводится полноценный пример функции для сообщений с типом данных `application/json`. Тип данных можно изменить в соответствии с потребностями.

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

Для проверки работы вы можете поместить сообщение в очередь с помощью средства [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). Приложение логики должно запуститься сразу же, как только функция получит это сообщение.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

<!---HONumber=AcomDC_0601_2016-->