<properties
	pageTitle="Обновление приложения мобильных служб до службы приложений Azure — Node.js"
	description="Узнайте, как без труда обновить приложение мобильных служб до мобильного приложения службы приложений."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="chrande"/>

# Обновление существующего приложения мобильной службы Azure Node.js до службы приложений

Мобильное приложение службы приложений — это новый способ сборки мобильных приложений с помощью Microsoft Azure. Дополнительные сведения см. в разделе [Что такое мобильные приложения?].

В этой статье приведен процесс обновления существующего серверного приложения Node.js мобильных служб Azure до нового мобильного приложения службы приложений. Во время обновления существующее приложение мобильных служб может продолжать работу.

При обновлении мобильного серверного приложения до службы приложений Azure оно получает доступ ко всем возможностям службы приложений. Тарификация при этом будет осуществляться в соответствии с [ценами службы приложений], а не мобильных служб.

## Миграция или обновление

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP]Перед обновлением рекомендуется [выполнить миграцию](app-service-mobile-migrating-from-mobile-services.md). Таким образом, к обеим версиям приложения будет применен один тарифный план службы, а дополнительные затраты будут исключены.

### Улучшения в пакете SDK сервера Node.js мобильных приложений

Обновление до нового [пакета SDK для мобильных приложений](https://www.npmjs.com/package/azure-mobile-apps) содержит множество улучшений, в частности:

- Новый упрощенный пакет SDK для Node, созданный на [платформе Express](http://expressjs.com/en/index.html), разработан специально для новых версий Node, которые будут выпускаться. Поведение приложения можно настроить с помощью ПО промежуточного слоя Express.

- Значительное повышение производительности по сравнению с пакетом SDK для мобильных служб.

- Веб-сайт можно разместить вместе с мобильным сервером. Аналогичным образом можно легко добавить пакет SDK для мобильных приложений Azure в любое существующее приложение expressv4.

- Пакет SDK для мобильных приложений предназначен для кроссплатформенной и локальной разработки. Поэтому приложения с его использованием можно разрабатывать и запускать локально на платформах Windows, Linux и OSX. Теперь стало удобнее использовать распространенные методы разработки Node (например, выполнение тестов [Mocha](https://mochajs.org/) перед развертыванием).

- Вы можете использовать Redis с собственными модулями, такими как [hiredis](https://www.npmjs.com/package/hiredis). В пакеты развертывания не нужно включать бинарные файлы, так как служба приложений установит пакеты npm для вас.

## <a name="overview"></a>Общие сведения об обновлении

В отличие от пакета SDK .NET для мобильных приложений обновление сервера Node от мобильных служб до мобильных приложений не ограничивается простой выгрузкой пакетов. Теперь целым стеком приложений управляете вы, а не Azure. Следовательно, вам необходимо создать базовое приложение Express для размещения мобильного сервера. Для таблицы и контроллеров API основные понятия схожи. Однако теперь необходимо экспортировать объекты таблицы, а API функций несколько изменились. Далее в этой статье будут рассматриваться основные стратегии обновления. Но прежде чем начинать миграцию, следует прочитать статью [Использование пакета SDK для Node для мобильных приложений Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md).

>[AZURE.TIP]Перед началом обновления рекомендуем полностью ознакомиться с этой темой. Запишите все используемые функции, которые вызываются ниже.

Пакеты SDK для клиента мобильных служб **несовместимы** с новым пакетом SDK для сервера мобильных приложений. Чтобы обеспечить непрерывность обслуживания приложения, не следует публиковать изменения на сайте, который в настоящее время обслуживает опубликованные клиенты. Вместо этого нужно создать новое мобильное приложение, которое является дубликатом. Это приложение можно включить в тот же план службы приложений, чтобы избежать дополнительных финансовых затрат.

После этого у вас будет две версии приложения: одна, которая остается без изменений и обслуживает опубликованные приложения на практике, и другая, которую можно обновить и ориентировать на новый выпуск клиента. Переносить и тестировать код можно в любое удобное для вас время. Однако перед этим необходимо убедиться, что внесенные исправления применены к обеим версиям. Если вы считаете, что до последней версии уже обновлено требуемое количество клиентских приложений, при желании можно удалить исходное перенесенное приложение. При размещении в одном плане службы приложений с мобильным приложением дополнительная плата не взимается.

Полная схема процесса обновления выглядит следующим образом.

1. Создайте новое мобильное приложение.
2. Обновите проект для использования новых пакетов SDK сервера.
3. Опубликуйте проект в новое мобильное приложение.
4. Выпустите новую версию клиентского приложения, которая использует новое мобильное приложение.
5. (Необязательно.) Удалите исходное перенесенное приложение мобильной службы.

Удаление может произойти при отсутствии трафика к исходному перенесенному приложению мобильной службы.

## <a name="mobile-app-version"></a> Начало обновления
Первый этап обновления — создание ресурса мобильного приложения, в котором будет размещаться новая версия вашего приложения. Если вы уже перенесли существующую мобильную службу, необходимо создать эту версию в том же плане размещения. Откройте [портал Azure] и перейдите к перенесенному приложению. Запишите план службы приложений, в котором оно работает.

### Создание второго экземпляра приложения
Далее мы создадим второй экземпляр приложения. Когда появится запрос на выбор плана службы приложений или «плана размещения», выберите план перенесенного приложения.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Скорее всего, вы захотите использовать ту же базу данных и концентратор уведомлений, которые использовали в мобильных службах. Вы можете скопировать эти значения. Для этого откройте [портал Azure], перейдите к исходному приложению и выберите **Параметры** > **Параметры приложения**. В разделе **Строки подключения** скопируйте `MS_NotificationHubConnectionString` и `MS_TableConnectionString`. Перейдите на новый сайт обновления и вставьте эти значения, перезаписав все существующие значения. Повторите эту процедуру для других необходимых параметров приложения. Если перенесенная служба не используется, строки подключения и параметры приложения вы можете просмотреть на вкладке **Настройка** раздела "Мобильные службы" на [портале Azure].

### Создание базовой серверной части мобильного приложения на Node

Каждая серверная часть на Node.js для мобильного приложения службы приложений Azure запускается как приложение ExpressJS. Создать базовое приложение [Express](http://expressjs.com/en/index.html) вы можете следующим образом.

1. В командной строке или в окне PowerShell создайте новый каталог для проекта.

        mkdir basicapp

2. Запустите команду npm init, чтобы инициализировать структуру пакета.

        cd basicapp
        npm init

    Для инициализации проекта командой npm init будет выведен ряд вопросов. См. пример выходных данных ниже.

    ![Выходные данные npm init][0]

3. Установите библиотеки express и azure-mobile-apps из репозитория npm.

        npm install --save express azure-mobile-apps

4. Создайте файл app.js для реализации базового мобильного сервера.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Important all tables in the 'tables' directory
        mobile.tables.import('./tables');
        mobile.api.import('./api');

        // Provide initialization of any tables that are statically defined
        mobile.tables.initialize().then(function () {
           // Add the mobile API so it is accessible as a Web API
           app.use(mobile);

           // Start listening on HTTP
           var port = process.env.PORT || 3000;
           app.listen(port, function () {
               console.log('Now listening on ', port)
           });
        });

Дополнительные примеры см. в нашем [репозитории GitHub](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples).

## Обновление серверного проекта

Мобильные приложения предоставляют новый [серверный пакет SDK для мобильных приложений], который имеет практически те же возможности, что и среда выполнения мобильных служб, но теперь вы являетесь полным владельцем среды выполнения. Мобильные приложения не требуют обновления версии Node или кода. Если вы выполнили описанные выше действия, у вас есть базовая версия мобильной среды выполнения Node. Теперь можно приступать к перемещению таблиц и логики API из мобильной службы в мобильное приложение. При этом необходимо настроить параметры сервера, включить push-уведомления, настроить проверку подлинности и многое другое.

### Базовая конфигурация

Сервер имеет множество параметров конфигурации, но многие из них имеют значения по умолчанию, что упрощает начало работы. Многие параметры будут настроены автоматически на [портале Azure] в меню **Данные**, **Проверка подлинности и авторизация** и **Push-уведомления**. Если для локальной разработки нужно использовать данные, проверку подлинности и push-уведомления, возможно, потребуется настроить локальную среду разработки.

Настройки сервера можно задать через переменные среды, которые настраиваются с помощью параметров приложения в серверной части мобильного приложения.

Вы можете выполнить дальнейшую настройку пакета SDK для мобильных приложений, передав [объект конфигурации](http://azure.github.io/azure-mobile-apps-node/global.html#configuration) инициализатору или [создав файл с именем azureMobile.js](app-service-mobile-node-backend-how-to-use-server-sdk/#howto-config-localdev) в корне проекта.

### Работа с данными и таблицами

Пакет SDK поставляется с поставщиком данных в памяти, что позволяет быстро и просто начать работу. Следует как можно раньше перейти на использование базы данных SQL, иначе при перезапуске поставщика данных в памяти все данные потеряются и экземпляры не будут согласованы.

Чтобы начать перемещение бизнес-логики из мобильной службы в мобильные приложения, нужно создать файл с именем таблицы (с расширением JS) в каталоге `./tables`. Полный пример таблицы мобильного приложения вы можете просмотреть на портале [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/todo/tables/TodoItem.js). Самая простая версия приведена ниже.

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    module.exports = table;

Чтобы начать перенос логики, для каждого файла `<tablename>.<operation>.js` необходимо иметь функцию для таблицы. Например, добавим функцию чтения.

В мобильной службе с таблицей TodoItem и операцией чтения, которая фильтрует элементы по идентификатору пользователя, функция будет выглядеть следующим образом:

    function(query, user, request) {
        query.where({ userId: user.userId});
        request.execute();
    }

Функция, которую мы добавляем в код таблицы мобильных приложений Azure, будет выглядеть так:

    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

Запросы и пользователь объединяются в контексте. В объекте контекста доступны следующие поля:

| Поле | Тип | Описание |
| :------ | :--------------------- | :---------- |
| запрос | queryjs/Query | Проанализированный запрос OData |
| id | string или number | Идентификатор, связанный с запросом |
| item | object | Вставляемый или удаляемый элемент |
| req | express.Request | Текущий объект запроса express |
| res | express.Response | Текущий объект ответа express |
| data | data | Настроенный поставщик данных |
| tables | function | Функция, которая принимает строку с названием таблицы и возвращает объект доступа к таблице |
| user | auth/user | Объект пользователя, прошедшего проверку подлинности. |
| results | object | Результаты выполнения операции |
| push | NotificationHubService | Служба центров уведомлений, если настроена |

Дополнительные сведения см. в [текущей документации по API](http://azure.github.io/azure-mobile-apps-node).

### CORS

CORS можно включить с помощью [параметра конфигурации CORS](http://azure.github.io/azure-mobile-apps-node/global.html#corsConfiguration) в пакете SDK.

Основные проблемы при использовании CORS заключаются в необходимости разрешить заголовки `eTag` и `Location` для правильной работы пакетов SDK клиента.

### Push-уведомления

Пакет SDK центров уведомлений Azure существенно обновился после мобильных служб, поэтому сигнатуры некоторых функций центров уведомлений могут отличаться. Во всем остальном функциональность аналогична мобильным службам. При наличии параметра приложения для центров уведомлений пакет SDK для мобильных приложений Azure предоставит экземпляр центров уведомлений и отобразит его в `context.push`. Пример можно найти на сайте [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/push-on-insert/tables/TodoItem.js) в соответствующем разделе, как показано ниже.

    table.insert(function (context) {
        // For details of the Notification Hubs JavaScript SDK,
        // see https://azure.microsoft.com/ru-RU/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/
        logger.silly('Running TodoItem.insert');

        // This push uses a template mechanism, so we need a template/
        var payload = '<toast><visual><binding template="Toast01"><text id="1">INSERT</text></binding></visual></toast>';

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    context.push.wns.send(null, payload, 'wns/toast', function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.silly('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
    });


### Запланированные задания
Запланированные задания не встраиваются в мобильные приложения. Поэтому все существующие задания в серверной части мобильной службы придется обновлять по отдельности. Один из вариантов — создать запланированное [веб-задание] на сайте кода мобильных приложений. Кроме того, вы можете настроить API, содержащий код задания, и задать в [планировщике Azure] вызов конечной точки по настроенному расписанию.

## <a name="authentication"></a>Рекомендации по проверке подлинности

Компоненты проверки подлинности мобильных служб перемещены в функцию проверки подлинности и авторизации службы приложений. Сведения о включении этой функции для сайта см. в разделе [Добавление проверки подлинности в мобильное приложение](app-service-mobile-ios-get-started-users.md).

При работе с некоторыми поставщиками, например AAD, Facebook и Google, требуется возможность использования существующей регистрации из копии приложения. Нужно просто перейти на портал поставщика удостоверений и добавить к регистрации новый URL-адрес перенаправления. Затем необходимо настроить проверку подлинности и авторизацию службы приложений с идентификатором клиента и секретом.

### Авторизация действий контроллера и удостоверение пользователя

Чтобы ограничить доступ к таблице, вы можете установить его на уровне таблицы с помощью параметра `table.access = 'authenticated';`. Полный пример см. на портале [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/authentication/tables/TodoItem.js).

Вы можете получить доступ к данным удостоверения пользователя с помощью метода `user.getIdentity`, описанного [здесь](http://azure.github.io/azure-mobile-apps-node/module-azure-mobile-apps_auth_user.html#~getIdentity).

## <a name="updating-clients"></a>Обновление клиентов
После получения рабочей серверной части мобильных приложений вы можете работать с новой версией клиентского приложения, которое использует эту серверную часть. Мобильные приложения также содержат новую версию клиентских пакетов SDK. Поэтому, как и при обновлении сервера, перед установкой версий мобильных приложений потребуется удалить все ссылки на пакеты SDK для мобильных служб.

Одно из основных различий между версиями заключается в том, что конструкторам больше не требуется ключ приложения. Просто передайте URL-адрес мобильного приложения. Например, в клиентах .NET конструктор `MobileServiceClient` теперь выглядит так:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Сведения об установке новых пакетов SDK и использовании новой структуры см. по ссылкам ниже:

- [iOS 3.0.0 или более поздней версии](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) 2.0.0 или более поздней версии](app-service-mobile-dotnet-how-to-use-client-library.md)

Если приложение использует push-уведомления, обратите внимание на особые инструкции по регистрации для каждой платформы, так как они немного изменились.

Когда новая версия клиента будет готова, испробуйте ее в новом обновленном серверном проекте. После проверки ее работы вы можете предоставить новую версию приложения клиентам. Когда как клиенты смогут получить эти обновления, версию мобильных служб приложения можно удалить. Этот шаг завершает обновление до мобильного приложения службы приложений с помощью последнего пакета SDK сервера мобильных приложений.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Что такое мобильные приложения?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /ru-RU/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[серверный пакет SDK для мобильных приложений]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[планировщике Azure]: /ru-RU/documentation/services/scheduler/
[веб-задание]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[ценами службы приложений]: https://azure.microsoft.com/ru-RU/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

[портал Azure]: https://portal.azure.com/
[портале Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/ru-RU/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/ru-RU/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_1223_2015-->