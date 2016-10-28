<properties 
	pageTitle="Триггеры приложения API службы приложений |Microsoft Azure" 
	description="Как реализовать триггеры в приложении API в службе приложений Azure" 
	services="logic-apps" 
	documentationCenter=".net" 
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="na" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="rachelap"/>

# Триггеры приложения API службы приложений Azure

>[AZURE.NOTE] Эта версия статьи предназначена для приложений API со схемой версии 2014-12-01-preview.


## Обзор

В этой статье описывается, как реализовать триггеры приложения API и использовать их из приложения логики.

Все фрагменты кода в этом разделе копируются из [примера кода приложения API FileWatcher](http://go.microsoft.com/fwlink/?LinkId=534802).

Обратите внимание, что необходимо загрузить следующий пакет nuget для создания и запуска кода в этой статье: [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/).

## Что такое триггеры приложения API?

Приложения API создают события, чтобы клиенты приложения API могли предпринять соответствующие действия в ответ на событие. Механизм на основе REST API, который поддерживает такой сценарий, называется триггер приложения API.

Например, предположим, что код клиента использует [приложение API с соединителем Twitter](../app-service-logic/app-service-logic-connector-twitter.md) и код должен выполнять определенное действие на основании новых твитов с конкретными словами. В таком случае можно настроить извещающий или опрашивающий триггер для упрощения этой задачи.

## Сравнение триггера опроса и извещающего триггера

На данный момент поддерживаются два типа триггеров:

- триггер опроса — клиент опрашивает приложение API для обнаружения уведомления о событии, которые было создано;
- извещающий триггер — клиент получает уведомление от приложения API, когда создается событие.

### Триггер опроса

Триггер опроса реализуется как обычный REST API и ожидает, что его клиенты (например, приложения логики) опросят его для получения уведомлений. Хотя клиент может сохранять состояние, сам триггер опроса не имеет состояния.

Следующая информация о пакетах запросов и ответов показывает некоторые ключевые аспекты контракта триггера опроса:

- Запрос
    - Метод HTTP: GET
    - Параметры
        - triggerState — этот необязательный параметр позволяет клиентам указать их состояние, чтобы триггер опроса мог правильно определить, следует ли возвращать уведомления, основываясь на указанном состоянии.
        - Параметры, относящиеся к API
- Ответ
    - Код состояния **200** — запрос действителен, и существует уведомление от триггера. Содержимое уведомления будет являться текстом ответа. Заголовок «Retry-After» в ответе указывает, что необходимо получить дополнительные данные уведомления с последующим вызовом запроса.
    - Код состояния **202** — запрос действителен, но нет новых уведомлений от триггера.
    - Код состояния **4xx** — запрос недействителен. Клиенту не следует повторять запрос.
    - Код состояния **5xx** — запрос привел к появлению внутренней ошибки сервера и/или временной проблемы. Клиенту следует повторить запрос.

В следующем фрагменте кода приведен пример реализации триггера опроса.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

Чтобы протестировать этот триггер опроса, выполните следующие действия.

1. Разверните приложение API с параметром проверки подлинности **общедоступный (анонимный)**.
2. Вызовите операцию **touch** для обращения к файлу. На следующем изображении приведен пример запроса через инструмент Postman. ![Вызов операции «touch» через Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. Вызовите триггер опроса, для которого значение параметра **triggerState** соответствует метке времени ранее шага 2. На следующем изображении приведен пример запроса через инструмент Postman. ![Вызов триггера опроса через Postman](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### Извещающий триггер

Извещающий триггер реализован в виде регулярного REST API, который отправляет уведомления на клиенты, зарегистрированные для получения уведомлений о возникновении определенных событий.

Следующая информация о пакетах запросов и ответов показывает некоторые ключевые аспекты контракта извещающего триггера.

- Запрос
    - Метод HTTP: PUT
    - Параметры
        - triggerId: обязательно — непрозрачная строка (например, GUID), представляющая регистрацию извещающего триггера.
        - callbackUrl: обязательно — URL-адрес обратного вызова, осуществляемого при возникновении события. Вызов — это простой вызов POST HTTP.
        - Параметры, относящиеся к API
- Ответ
    - Код состояния **200** — запрос на регистрацию клиента успешно завершен.
    - Код состояния **4xx** — запрос недействителен. Клиенту не следует повторять запрос.
    - Код состояния **5xx** — запрос привел к появлению внутренней ошибки сервера и/или временной проблемы. Клиенту следует повторить запрос.
- Обратный вызов
    - Метод HTTP: POST
    - Текст запроса: содержимое уведомления.

В следующем фрагменте кода приведен пример реализации извещающего триггера.

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

Чтобы протестировать этот триггер опроса, выполните следующие действия.

1. Разверните приложение API с параметром проверки подлинности **общедоступный (анонимный)**.
2. Перейдите к [http://requestb.in/](http://requestb.in/) для создания RequestBin, который будет использоваться в качестве URL-адрес обратного вызова.
3. Вызовите извещающий триггер со значением идентификатора GUID **triggerId** и URL-адресом RequestBin — **callbackUrl**. ![Вызов извещающего триггера через Postman](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. Вызовите операцию **touch** для обращения к файлу. На следующем изображении приведен пример запроса через инструмент Postman. ![Вызов операции «touch» через Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. Проверьте RequestBin, чтобы убедиться, что обратный вызов извещающего триггера выполняется с выводом свойства. ![Вызов триггера опроса через Postman](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### Описание триггеров в определении API

После реализации триггеров и развертывания приложения API в Azure перейдите к колонке **Определение API** на портале предварительной версии Azure и вы увидите, что триггеры автоматически распознаются в пользовательском интерфейсе, управляемом с помощью определения Swagger 2.0 приложения API.

![Колонка определения API](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

Если нажать кнопку **Загрузить Swagger** и открыть файл JSON, вы увидите результаты, аналогичные следующим:

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

Свойство расширения **x-ms-schedular-trigger** определяет, как триггеры описаны в определении API, оно автоматически добавляется на шлюзе приложения API при запросе определения API через шлюз, если запрос осуществляется к одному из следующих критериев. (Это свойство также можно добавить вручную.)

- Триггер опроса
    - Если используется метод HTTP **GET**.
    - Если свойство **operationId** содержит строку **trigger**.
    - Если свойство **parameters** включает параметр со свойством **name**, для которого установлено значение **triggerState**.
- Извещающий триггер
    - Если используется метод HTTP **PUT**.
    - Если свойство **operationId** содержит строку **trigger**.
    - Если свойство **parameters** включает параметр со свойством **name**, для которого установлено значение **triggerId**.

## Использование триггеров приложения API в приложениях логики

### Перечисление и настройка триггеров приложения API в конструкторе приложений логики

При создании приложения логики в той же группе ресурсов, что и приложение API, можно добавить его на полотно конструктора, просто щелкнув его. Это показано на следующих изображениях.

![Триггеры в конструкторе приложения логики](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![Настройка триггера опроса в конструкторе приложения логики](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![Настройка извещающего триггера в конструкторе приложения логики](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## Оптимизация триггеров приложения API для приложений логики

После добавления триггеров в приложение API можно выполнить несколько действий для более удобного использования приложения API в приложении логики.

Например, для параметра **triggerState** триггеров опроса должно быть присвоено следующее выражение в приложении логики. Это выражение должно оценивать последний вызов триггера из приложения логики и возвращают данное значение.

	@coalesce(triggers()?.outputs?.body?['triggerState'], '')

Примечание. Объяснение функций, используемых в выражении выше, см. документацию о [языке определения рабочего процесса приложения логики](https://msdn.microsoft.com/library/azure/dn948512.aspx).

Пользователям приложения логики необходимо указать упомянутое выше выражение для параметра **triggerState** при использовании триггера. Это значение может быть предварительно задано с помощью конструктора приложения логики через свойство расширения **x-ms-scheduler-recommendation**. Свойству расширения **x-ms-visibility** может быть присвоено значение *internal*, чтобы сам параметр не отображался в конструкторе. Это показано в следующем фрагменте.

    "/api/Messages/poll": {
      "get": {
	    "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

Для извещающих триггеров параметр **triggerId** должен однозначно определять приложение логики. Рекомендуется присвоить этому свойству имя рабочего процесса с помощью следующего выражения:

    @workflow().name

С помощью свойств расширения **x-ms-scheduler-recommendation** и **x-ms-visibility** в его определении API приложение API может настроить конструктор приложения логики на автоматическую установку этого выражения для пользователя.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### Добавление свойств расширения в определение API

В определение API можно добавить дополнительные сведения о метаданных, например свойства расширения **x-ms-scheduler-recommendation** и **x-ms-visibility**, одним из двух способов: статическим или динамическим.

Для статических метаданных можно непосредственно редактировать файл */metadata/apiDefinition.swagger.json* в проекте и добавить свойства вручную.

Для приложений API, использующих динамические метаданные, можно изменить файл SwaggerConfig.cs и добавить фильтр операции, который может добавить эти расширения.

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


Ниже приведен пример реализации этого класса для упрощения сценария с динамическими метаданными.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }
 

<!---HONumber=AcomDC_0831_2016-->