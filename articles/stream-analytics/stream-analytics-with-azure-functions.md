---
title: "Запуск функций Azure с помощью задания Azure Stream Analytics | Документы Microsoft"
description: "Подробные сведения о настройке функции Azure в качестве приемника выходных данных потока аналитической заданиями."
keywords: "выходные данные, данные потоковой передачи данных, функция Azure"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: adc147fc9f47e78cc0a2fcaf53f064bcfa5eee2c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>Запуск функций Azure с помощью задания Azure Stream Analytics 
 
> [!IMPORTANT]
> Эта функция доступна в режиме предварительной версии.

Функции Azure можно выполнить с Azure Stream Analytics, настроив функции Azure как один из приемники выходных данных в задание Stream Analytics. Функция Azure является управляемых событиями, взаимодействие вычислений по требованию, позволяющие реализовать код, который запускается событиями, происходящими в Azure или сторонних служб. Эта возможность функции Azure реагировать на триггеры упрощает естественным вывода задания Azure Stream Analytics.

Azure Stream Analytics вызывает функцию Azure посредством HTTP триггеров. Выходной адаптер функция Azure позволяет пользователям подключаться функции Azure Stream Analytics, таким образом, что события может запускаться на основе запросов Stream Analytics. 

Этот учебник демонстрирует процедуру подключения Azure Stream Analytics для [кэш Azure Redis](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md) с помощью [функции Azure](../azure-functions/functions-overview.md). 

## <a name="configure-stream-analytics-job-to-run-an-azure-function"></a>Настройка задания Stream Analytics для выполнения функции Azure 

В этом разделе показано, как настроить задание Stream Analytics, чтобы выполнить это функция Azure, которая записывает данные в кэше Redis для Azure. Задание Stream Analytics считывает события из концентратора событий, выполняет запрос, который вызывает функцию Azure. Эта функция Azure считывает данные из задания Stream Analytics и записывает его в кэш Azure Redis.

![График, чтобы проиллюстрировать учебника](./media/stream-analytics-with-azure-functions/image1.png)

Для этого необходимы следующие действия:
* [Создайте задание Stream Analytics с концентратором событий в качестве входного.](#create-stream-analytics-job-with-event-hub-as-input)  
* [Создайте кэш Redis для Azure.](#create-an-azure-redis-cache)  
* [Создайте функцию Azure, которая может записывать данные в кэш Redis.](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Обновите задание аналитической потока с помощью функции Azure в качестве выходных данных.](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Результаты проверки кэша Redis.](#check-redis-cache-for-results)  

## <a name="create-stream-analytics-job-with-event-hub-as-input"></a>Создайте задание Stream Analytics с концентратора событий в качестве входных данных

Выполните [в режиме реального времени мошенничества](stream-analytics-real-time-fraud-detection.md) учебника, чтобы создать концентратор событий, запуск приложения генератора событий и создания Azure Stream Analytics (пропустить шаги для создания запроса и вывод, вместо этого будет программой установки Функции Azure выходные данные в следующем разделе.)

## <a name="create-an-azure-redis-cache"></a>Создание кэша Redis для Azure

1. Создание с помощью действия, описанные в кэш Azure Redis [создать кэш](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache) статьи кэша Redis.  

2. После создания кэша Redis, перейдите к созданной кэша > **клавиши доступа** > и запишите **основная строка подключения**.

   ![Строка подключения для кэша redis](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-an-azure-function-that-can-write-data-to-the-redis-cache"></a>Создайте функцию Azure, которая может записывать данные в кэш Redis

1. Используйте [создания функции приложения](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) раздел документации функции Azure для создания приложения Azure, функции и [функции активации HTTP Azure](../azure-functions/functions-create-first-azure-function.md#create-function) (также называемого веб-перехватчика) с помощью **CSharp** языка.  

2. Перейдите к **run.csx** функции и обновить его с помощью следующего кода (необходимо заменить «\<строки подключения кэша redis здесь\>"текста с помощью кэша Redis основная строка подключения что вы получили в предыдущем разделе):  

   ```c#
   using System;
   using System.Net;
   using System.Threading.Tasks;
   using StackExchange.Redis;
   using Newtonsoft.Json;
   using System.Configuration;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
      // Get the request body
      dynamic dataArray = await req.Content.ReadAsAsync<object>();

      // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

      if (dataArray.ToString().Length > 262144)
      {        
         return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
      log.Info($"Connection string.. {connection}");
    
      // Connection refers to a property that returns a ConnectionMultiplexer
      IDatabase db = connection.GetDatabase();
      log.Info($"Created database {db}");
    
      log.Info($"Message Count {dataArray.Count}");

      // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
      for (var i = 0; i < dataArray.Count; i++)
      {
        string time = dataArray[i].time;
        string callingnum1 = dataArray[i].callingnum1;
        string key = time + " - " + callingnum1;
        db.StringSet(key, dataArray[i].ToString());
        log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
      // Simple get of data types from the cache
      string value = db.StringGet(key);
      log.Info($"Database got: {value}");
      }

      return req.CreateResponse(HttpStatusCode.OK, "Got");
}    

   ```

   Когда Azure Stream Analytics получает 413 (Http слишком большой объект запроса) исключения из функции Azure, это уменьшает размер пакетов, передаваемые функциям Azure. В функции Azure используйте следующий код, чтобы проверить, что Azure Stream Analytics не отправляет пакеты слишком большого размера. Убедитесь, что пакета максимальное число и размер значения, используемые в функции соответствуют значения, введенные на портале Stream Analytics.

   ```c#
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. В текстовом редакторе по своему усмотрению, создания JSON-файла с именем **project.json** следующим кодом и сохраните его на локальном компьютере. Этот файл содержит зависимости пакетов NuGet, необходимых для функции c#.  
   
   ```json
       {
         "frameworks": {
             "net46": {
                 "dependencies": {
                     "StackExchange.Redis":"1.1.603",
                     "Newtonsoft.Json": "9.0.1"
                 }
             }
         }
     }

   ```
 
4. Вернитесь на портал Azure > перейдите к Azure функции > из **возможности платформы** вкладка > щелкните **редактор службы приложения** , расположенной в **средства разработки**. 
 
   ![Экран Редактор службы приложения](./media/stream-analytics-with-azure-functions/image3.png)

5. В редакторе приложения службы, щелкните правой кнопкой мыши корневой каталог и отправка **project.json** файла. После успешной загрузки, обновите страницу, вы увидите автоматически созданный файл с именем **project.lock.json**.  Автоматически созданный файл содержит ссылки на библиотеки DLL, которые указаны в файле Project.json.  

   ![Загрузить файл project.json](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytic-job-with-azure-function-as-output"></a>Обновить задание аналитической потока с помощью функции Azure в качестве выходных данных

1. Откройте задание Azure Stream Analytics на портале Azure.  

2. Перехода к функции Azure > **Обзор** > **выходов** > **добавить** новый выход > выберите **Azure функция** для параметра приемника. Новый выходной адаптер функция Azure доступен со следующими свойствами:  

   |**Имя свойства**|**Описание**|
   |---|---|
   |Псевдоним вывода| Понятное имя, которое используется в запросе задания ссылка выходные данные. |
   |Параметр импорта| Можно использовать функции azure из текущей подписки или указать параметры вручную, если функция находится в другой подписке. |
   |Приложение-функция| Имя приложения Azure функции. |
   |Функция| Имя функции в приложении функции (имя этой функции run.csx).|
   |Максимальный размер пакета|Это свойство используется для задания максимального размера для каждого пакета выходных данных, отправленных на функции в Azure. По умолчанию это значение равным 256 КБ.|
   |Максимальное количество пакетов|Это свойство позволяет указать максимальное число событий в каждом пакете, отправленном функции Azure. Значение max пакета по умолчанию — 100. Это необязательное свойство.|
   |Ключ|Это свойство позволяет использовать функцию из другой подписки Azure. Укажите значение ключа для доступа к функции. Это необязательное свойство.|

3. Введите имя для псевдонима выхода. В этом учебнике мы имя **saop1** (можно использовать любое имя по своему усмотрению) и заполните другие сведения.  

4. Откройте ваше задание Stream Analytics и обновить запрос для следующих (не забудьте заменить текст «saop1», если различными именами приемник выходных данных):  

   ```sql
    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Запустите приложение telcodatagen.exe, выполнив следующую команду в командной строке (команда принимает формат - `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`)  
   
   **telcodatagen.exe 1000.2 2**
    
6.  Запуск задания Stream Analytics.

## <a name="check-redis-cache-for-results"></a>Результаты проверки кэша Redis

1. Перейдите на портал Azure и найдите кэша Redis > выберите **консоли**.  

2. Используйте [команды кэша Redis](https://redis.io/commands) для проверки правильности данных в кэше Redis. (Команда принимает формат Get {ключ}). Например: 

   **Получение «12/19/2017 г. 21:32:24-123414732»**

   Эта команда должна вывести значение для указанного ключа:

   ![Выходные данные кэша redis](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Известные проблемы

* На портале Azure, при попытке сбросить размер пакета Max-значение максимальное количество пакетов для empty(default), он изменяет на ранее введенное значение после сохранения. Намеренно введите значения по умолчанию для этих полей в этом случае.

## <a name="next-steps"></a>Дальнейшие действия
