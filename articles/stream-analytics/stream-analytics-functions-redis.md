<properties
	pageTitle="Вывод данных из Azure Stream Analytics в кэш Redis для Azure с помощью функций Azure | Microsoft Azure"
	description="Узнайте, как использовать функцию Azure, подключенную к очереди служебной шины, для заполнения кэша Redis для Azure выходными данными задания Stream Analytics."
	keywords="Поток данных, кэш Redis, очередь служебной шины"
	services="stream-analytics"
	authors="ryancrawcour"
	manager="jhubbard"
    documentationCenter=""
	/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/09/2016"
	ms.author="ryancraw"/>

# Как сохранять данные из Azure Stream Analytics в кэш Redis для Azure с помощью функций Azure

Azure Stream Analytics обеспечивает быстрое развертывание и внедрение бюджетных аналитических решений для анализа в реальном времени данных с устройств, датчиков, из инфраструктуры и приложений, а также любого потока данных. Azure Stream Analytics обеспечивает различные варианты использования, в том числе управление и мониторинг в реальном времени, контроль и управление, обнаружение мошенничества, автомобили с сетевыми возможностями и многое другое. Во многих подобных сценариях может потребоваться сохранять данные, выдаваемые Azure Stream Analytics, в распределенном хранилище данных, например кэш Redis для Azure.

Предположим, что вы работаете в телекоммуникационной компании. Вы пытаетесь обнаружить мошенничество с SIM-картами, при котором один человек выполняет несколько вызовов в одно и то же время, но из разных географических расположений. Перед вами поставили задачу сохранять все потенциальные мошеннические телефонные звонки в кэше Redis для Azure. В этом блоге мы опишем, как можно легко выполнить вашу задачу.

## Предварительные требования
Изучите пошаговое руководство по [выявлению мошенничества в реальном времени][fraud-detection] для ASA.

## Общие сведения об архитектуре
![Снимок экрана: архитектура](./media/stream-analytics-functions-redis/architecture-overview.png)

Как показано на предыдущем рисунке, Stream Analytics позволяет запрашивать потоковые входные данные и отправлять в качестве выходных данных. Затем, в зависимости от выходных данных, функции Azure могут активировать события определенного типа.

В этом блоге мы рассмотрим функции Azure, входящие в этот конвейер, а точнее — активацию события, которое сохраняет мошеннические данные в кэш. После изучения учебника [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени][fraud-detection] у вас должны быть уже настроены рабочие входные данные (концентратор событий), запрос и выходные данные (хранилище BLOB-объектов). В этом блоге мы изменим выходные данные, чтобы вместо них использовать очередь служебной шины. После этого мы подключим функцию Azure к этой очереди.

## Создание и подключение выходных данных очереди служебной шины
Чтобы создать очередь служебной шины, выполните шаги 1 и 2 раздела о .NET статьи [Начало работы с очередями служебной шины][servicebus-getstarted]. Теперь подключим очередь к заданию Stream Analytics, которое было создано в более раннем пошаговом руководстве по выявлению мошенничества.



1. На портале Azure перейдите к колонке **Выходные данные** своего задания и выберите **Добавить** в верхней части страницы.

	![Добавление выходных данных](./media/stream-analytics-functions-redis/adding-outputs.png)

2. Выберите значение **Очередь служебной шины** для параметра **Приемник** и следуйте инструкциям на экране. Не забудьте выбрать пространство имен очереди служебной шины, созданное при изучении учебника [Начало работы с очередями служебной шины][servicebus-getstarted]. По завершении нажмите кнопку со стрелкой вправо.
3. Укажите следующие значения:
	- **Формат сериализатора событий**: JSON;
	- **Кодировка**: UTF8.
	- **ФОРМАТ**: строки-разделители.

4. Нажмите кнопку **Создать**, чтобы добавить этот источник и убедиться, что служба Stream Analytics может успешно подключаться к учетной записи хранения.

5. На вкладке **Запрос** замените текущий запрос следующим. Замените *[YOUR SERVICE BUS NAME]* именем выходных данных, созданных на шаге 3.

    ```    

	    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
	    JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## Создание кэша Redis для Azure
Создайте кэш Redis для Azure, следуя указаниям из раздела о .NET статьи [Как использовать кэш Redis для Azure][use-rediscache] вплоть до раздела ***Настройка клиентов кэша***. После выполнения всех указаний у вас будет новый кэш Redis. В разделе **Все параметры** выберите **Ключи доступа** и запишите значение ***Первичная строка подключения***.

![Снимок экрана: архитектура](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## Создание функции Azure
Следуйте указаниям учебника [Приступая к работе с Azure][functions-getstarted], чтобы начать работу с функциями Azure. Если у вас уже имеется функция Azure, которую вы бы хотели использовать, перейдите к разделу [Запись в кэш Redis](#Writing-to-Redis-Cache).

1. На портале выберите "Службы приложений" в области навигации слева, затем щелкните имя своего приложения-функции Azure, чтобы получить веб-сайт приложения-функции. ![Снимок экрана: список функций службы приложений](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. Щелкните **Создать функцию > ServiceBusQueueTrigger — C#**. Для заполнения следующих полей выполните приведенные ниже действия.
	- **Имя очереди**. Имя, введенное при создании очереди в учебнике [Начало работы с очередями служебной шины][servicebus-getstarted] \(не имя служебной шины). Убедитесь, что используется очередь, подключенная к выходным данным Stream Analytics.
	- **Подключение к служебной шине**. Выберите **Добавить строку подключения**. Чтобы найти строку подключения, перейдите на классический портал, выберите **Служебная шина**, затем выберите служебную шину, которую вы создали, и щелкните **СВЕДЕНИЯ О ПОДКЛЮЧЕНИИ** в нижней части экрана. Убедитесь, что на этой странице отображается главный экран. Скопируйте и вставьте строку подключения. Вы можете ввести любое имя подключения.
	
		![Снимок экрана: подключение к служебной шине](./media/stream-analytics-functions-redis/servicebus-connection.png)
	- **AccessRights**: выберите **Управление**.


3. Нажмите кнопку **Создать**

## Запись в кэш Redis
Мы создали функцию Azure, которая считывает данные из очереди служебной шины. Все, что осталось сделать, — использовать нашу функцию для записи данных в кэш Redis.

1. Выберите только что созданный **ServiceBusQueueTrigger** и щелкните **Function app settings** (Параметры приложения-функции) в правом верхнем углу. Выберите "Go to App Service Settings" (Перейти к параметрам службы приложений) > **Параметры > Параметры приложения**.

2. В разделе "Строка подключения" создайте имя в разделе **Имя**. Вставьте первичную строку подключения, записанную на шаге **Создание кэша Redis**, в раздел **Значение**. Выберите значение **Пользовательская** из списка со значением **База данных SQL**.

3. Щелкните **Сохранить** в верхней части страницы.

	![Снимок экрана: подключение к служебной шине](./media/stream-analytics-functions-redis/function-connection-string.png)

4. Теперь вернитесь к параметрам службы приложений и выберите **Средства > Редактор службы приложений (предварительная версия) > Вкл. > Перейти**.

	![Снимок экрана: подключение к служебной шине](./media/stream-analytics-functions-redis/app-service-editor.png)

5. В редакторе по своему усмотрению создайте JSON-файл **project.json** с приведенным ниже содержимым и сохраните его на локальный диск.

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

6. Передайте этот файл в корневой каталог своей функции (не WWWROOT). Автоматически должен появиться файл **project.lock.json**. Его появление подтверждает, что пакеты Nuget StackExchange.Redis и Newtonsoft.Json были импортированы.

7. В файле **run.csx** замените предварительно созданный код приведенным ниже кодом. В функции lazyConnection замените CONN NAME именем, созданным на шаге 2 процедуры **сохранения данных в кэш Redis**.

````

	using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
		new Lazy<ConnectionMultiplexer>(() =>
    		{
				var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
        		return ConnectionMultiplexer.Connect();
    		});
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## Запуск задания Stream Analytics

1. Запустите приложение telcodatagen.exe. Оно используется следующим образом. ````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. В колонке задания Stream Analytics на портале щелкните **Запустить** в верхней части страницы.

	![Снимок экрана: запуск задания](./media/stream-analytics-functions-redis/starting-job.png)

3. В отобразившейся колонке **Запуск задания** выберите **Сейчас** и нажмите кнопку **Запустить** в нижней части экрана. Состояние задания изменится на "Запускается", а спустя некоторое время — на "Выполняется".
 
	![Снимок экрана: выбора времени начала задания](./media/stream-analytics-functions-redis/start-job-time.png)

## Запуск решения и проверка результатов
Вернувшись на страницу **ServiceBusQueueTrigger**, вы должны увидеть журнал инструкций. В этих журналах показано, что данные были получены из очереди служебной шины, помещены в базу данных и получены из нее с использованием времени в качестве ключа!

Чтобы убедиться, что данные находятся в кэше Redis, перейдите на страницу кэша Redis на новом портале (как показано на предыдущем шаге [Создание кэша Redis для Azure](#Create-an-Azure-Redis-Cache)) и выберите "Консоль".

Теперь можно ввести команды Redis, чтобы убедиться, что данные на самом деле находятся в кэше.

![Снимок экрана: консоль Redis](./media/stream-analytics-functions-redis/redis-console.png)

## Дальнейшие действия
Мы рады новым возможностям, которые обеспечивает совместное использование функций Azure и Stream Analytics, и надеемся, что это откроет новые возможности для вас. Если вы хотите оставить отзыв о том, что хотели бы получить в дальнейшем, то можете воспользоваться [сайтом Azure UserVoice](https://feedback.azure.com/forums/270577-stream-analytics).

Если вы еще не работали с Microsoft Azure, то мы предлагаем вам ознакомиться с этой средой, зарегистрировавшись для получения [бесплатной пробной учетной записи Azure](https://azure.microsoft.com/pricing/free-trial/). Если вы не работали со Stream Analytics, то мы предлагаем вам [создать свое первое задание Stream Analytics](stream-analytics-create-a-job.md).

Если вам нужна помощь или ответы на вопросы, напишите об этом на форуме [MSDN](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics) или [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics).

Кроме того, вы можете ознакомиться со следующими материалами.

- [Справочник разработчика по функциям Azure](../azure-functions/functions-reference.md)
- [Справочник разработчика C# по функциям Azure](../azure-functions/functions-reference-csharp.md)
- [Справочник разработчика F# по функциям Azure](../azure-functions/functions-reference-fsharp.md)
- [Справочник разработчика NodeJS по функциям Azure](../azure-functions/functions-reference.md)
- [Триггеры и привязки в функциях Azure](../azure-functions/functions-triggers-bindings.md)
- [Как отслеживать кэш Redis для Azure](../redis-cache/cache-how-to-monitor.md)

Чтобы быть в курсе последних новостей и функций, подпишитесь на [@AzureStreaming](https://twitter.com/AzureStreaming) в Twitter.


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md

<!---HONumber=AcomDC_0921_2016-->