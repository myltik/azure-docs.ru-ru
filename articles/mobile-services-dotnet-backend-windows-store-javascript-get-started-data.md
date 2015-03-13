<properties 
	pageTitle="Начало работы с данными (Магазин Windows) | Центр разработчиков для мобильных устройств" 
	description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении магазина Windows." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="wesmc"/>

# Добавление мобильных служб к существующему приложению

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

В этом разделе показано, как использовать мобильные службы Azure в качестве серверного источника данных для приложения из Магазина Windows. В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для приложения, хранящего данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

В этом учебнике речь идет о создании серверной мобильной службы Microsoft .NET. Серверная часть .NET позволяет использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе, и вы можете запускать и отлаживать мобильную службу на локальном компьютере. Сведения о создании мобильной службы, которая позволяет реализовать серверную бизнес-логику в JavaScript, см. в подразделе "Серверная версия JavaScript" этого раздела.

>[AZURE.NOTE]В этом разделе также показано, как добавить мобильные службы Azure в проект Магазина Windows. Вы можете использовать инструменты Visual Studio 2013, чтобы добавить ту же серверную мобильную для .NET в проект универсального приложения для Windows. Дополнительные сведения см. в [версии этого учебника для универсального приложения Windows](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data).

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения Магазина Windows]
2. [Создание мобильной службы]
3. [Локальная загрузка мобильной службы]
4. [Обновление приложения Магазина Windows для использования мобильной службы]
5. [Тестирование приложения Магазина Windows для службы, размещенной локально]
6. [Публикация мобильной службы в Azure]
7. [Тестирование приложения Магазина Windows с помощью службы, размещенной в Azure]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Доступна бесплатная пробная версия.

##<a name="download-app"></a>Загрузка проекта GetStartedWithData

Этот учебник создан на основе [приложения GetStartedWithMobileServices][Веб-сайт с примерами кода для разработчиков], являющегося проектом приложения для Магазина Windows в Visual Studio 2013. Пользовательский интерфейс для этого приложения похож на интерфейс приложения, созданного в кратком руководстве по использованию мобильных служб. Отличие заключается в том, что добавленные элементы хранятся локально в памяти. 

1. Загрузите версию примера приложения GetStartedWithMobileServices на JavaScript с [веб-сайта с примерами кода для разработчиков]. 

2. Запустите Visual Studio 2013 с правами администратора, для чего щелкните правой кнопкой мыши Visual Studio и выберите пункт **Запуск от имени администратора**.

3. В Visual Studio 2013 откройте загруженный проект. В обозревателе решений разверните папку js и изучите файл default.js.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся во внутренней памяти в `WinJS.Binding.List`.

4. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

5. В приложении введите какой-либо текст в расположенном ниже поле **Insert a TodoItem** (Вставка TodoItem), затем щелкните **Сохранить**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запрос и обновление данных**

##<a name="create-service"></a>Создание мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Загрузка проекта мобильной службы и его добавление в решение

1. На [портале управления Azure] щелкните новую мобильную службу или вкладку с ее облачным значком, чтобы перейти на страницу обзора.

    ![][2]

2. Щелкните платформу **Магазин Windows**. В разделе **Начало работы** разверните элемент **Подключение существующего приложения Магазина Windows**, а затем щелкните **Загрузить**, чтобы загрузить персональный начальный проект для мобильной службы. 

    ![][3]

3. Выполните прокрутку вниз раздела **Приступая к работе** до шага **Публикация службы в облаке**. Щелкните ссылку, показанную на представленном ниже снимке экрана, чтобы загрузить файл профиля публикации для только что загруженной мобильной службы.

    > [AZURE.NOTE] Сохраните файл в безопасном месте, поскольку он содержит важные сведения, относящиеся к вашей учетной записи Azure. Вы удалите этот файл после публикации мобильной службы позднее в этом учебнике. 

    ![][5]


4. Распакуйте скачанный персонализированный начальный проект службы. Скопируйте папки, находившиеся в ZIP-файле, в один каталог **JavaScript**, в котором находится файл решения для начала работы с данными (SLN-файл). Так диспетчеру пакетов NuGet будет проще обеспечивать синхронизацию всех пакетов. 

    ![][26]

5. В обозревателе решений Visual Studio щелкните правой кнопкой мыши свое решение для начала работы с приложением обработки данных для Магазина Windows. Щелкните **Добавить**, затем щелкните **Существующий проект**.

    ![][4]

6. В диалоговом окне "Добавление существующего проекта" перейдите к папке проекта мобильной службы, которую вы перенесли в каталог **JavaScript**. В подкаталоге службы выберите файл проекта C# (с расширением .csproj). Щелкните **Открыть**, чтобы добавить проект в решение.

    ![][6]

7. В обозревателе решений Visual Studio щелкните правой кнопкой мыши только что добавленный проект службы и выберите пункт **Сборка**, чтобы убедиться, что сборка выполняется без ошибок. Во время сборки диспетчеру пакетов NuGet может потребоваться восстановить некоторые пакеты NuGet, на которые имеются ссылки в проекте.

    ![][20]

8. Еще раз щелкните правой кнопкой мыши проект службы. На этот раз выберите команду **Запустить новый экземпляр** в контекстном меню **Отладка**.

    ![][21]

    В Visual Studio открывается веб-страница по умолчанию для вашей службы. Щелкните **Попробовать сейчас**, чтобы с веб-страницы по умолчанию протестировать методы в мобильной службе.

    ![][22]

    Visual Studio по умолчанию размещает мобильную службу локально в IIS Express. Чтобы увидеть это, щелкните правой кнопкой мыши значок для IIS Express на панели задач.

    ![][23]


#<a name="update-app"></a>Обновление приложения Магазина Windows для использования мобильной службы

В этом разделе вы обновите приложение Магазина Windows, чтобы использовать мобильную службу в качестве серверной службы для приложения.


1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект приложения Магазина Windows и выберите пункт **Управление пакетами NuGet**.

    ![][7]

2. В диалоговом окне "Управление пакетами NuGet" выполните поиск **WindowsAzure.MobileServices.WinJS** в коллекции сетевых пакетов и щелкните, чтобы установить Nuget-пакет мобильных служб Azure. Затем закройте диалоговое окно.

    ![][8]

3. Вернитесь на страницу обзора мобильной службы на портале управления Azure и найдите действие **Подключение приложения и хранение данных в службе**. Выберите **JavaScript** в качестве языка и скопируйте фрагмент кода для создания объекта `MobileServiceClient`.

    ![][9]

4. В обозревателе решений Visual Studio разверните папку **js** и откройте файл default.js. Вставьте скопированный фрагмент кода в обработчик событий `app.onactivated` прямо перед определением для переменной `todoItems`. Заметьте, что фрагмент кода включает закомментированный конструктор для подключения к мобильной службе в Azure с использованием ключа приложения. Он будет раскомментирован в дальнейшем.

    ![][10]


5. В файле default.js замените оставшийся код для обработчика событий `app.onactiviated` приведенным ниже кодом, определяющим переменную `todoItems` и операции, которые мы будем тестировать с мобильной службой. Затем сохраните файл.

    Этот код использует пакет Mobile Services SDK для JavaScript, чтобы приложение могло хранить данные в таблице, предоставленной службой, а не размещать их локально в памяти. Три основных метода: are `insertTodoItem`, `refreshTodoItems` и `updateCheckedTodoItem`. Эти три метода позволяют асинхронно вставлять, запрашивать и обновлять коллекцию данных в таблице в Azure.

            var todoTable = client.getTable('TodoItem');
            var todoItems = new WinJS.Binding.List();
            var insertTodoItem = function (todoItem) {
                // This code inserts a new TodoItem into the database. 
                // Mobile Services assigns each item an id and the 
                // item is added to the Binding List
                todoTable.insert(todoItem).done(function (item) {
                    todoIte
	ms.push(item);
                });
            };
            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view 
                // by querying the TodoItems table.
                todoTable.where()
                    .read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listIte
	ms.winControl.itemDataSource = todoIte
	ms.dataSource;
                    });
            };
            var updateCheckedTodoItem = function (todoItem) {
                // This code updates the database. 
                // When the MobileService responds, 
                // the item is removed from the list 
                todoTable.update(todoItem).done(function (item) {
                    todoIte
	ms.splice(todoIte
	ms.indexOf(item), 1);
                });
            };
            buttonSave.addEventListener("click", function () {
                insertTodoItem({
                    text: textInput.value,
                    complete: false
                });
            });
            buttonRefresh.addEventListener("click", function () {
                refreshTodoItems();
            });
            listIte
	ms.addEventListener("change", function (eventArgs) {
                var todoItem = eventArgs.target.dataContext.backingData;
                todoItem.complete = eventArgs.target.checked;
                updateCheckedTodoItem(todoItem);
            });
            refreshTodoItems();

6. В обозревателе решений в Visual Studio откройте файл default.html. В верхней части файла добавьте новую ссылку на скрипт WinJS для MobileServices.js. Затем сохраните файл.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][19]

##<a name="test-locally-hosted"></a>Тестирование приложения для Магазина Windows для службы, размещенной локально

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

##<a name="publish-mobile-service"></a>Публикация мобильной службы в Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Тестирование мобильной службы, опубликованной в Azure

1. В обозревателе решений Visual Studio разверните папку **js** и откройте файл default.js.  Закомментируйте код, который создает объект `MobileServiceClient`, подключающийся к локально размещенной мобильной службе. Раскомментируйте код, который создает объект `MobileServiceClient`, подключающийся к вашей службе в Azure. Сохраните изменения в файле.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);	
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


2. В Visual Studio нажмите клавишу F5 или щелкните **Начать отладку** в меню "Отладка". В результате перед запуском приложения для подключения к мобильной службе, размещенной удаленно в Azure, выполняется повторная сборка приложения Магазина Windows с учетом предыдущего изменения. 

    ![][12]


3. Введите новые элементы todoitem и для каждого из них нажмите кнопку **Сохранить**. Установите флажок для завершения некоторых новых элементов. Каждый новый элемент todoItem будет храниться и обновляться в базе данных SQL, ранее настроенной вами для мобильной службы на портале управления Azure. 

    ![][16]

    Можно перезапустить приложение, чтобы убедиться, что изменения были сохранены в базе данных в Azure. Можно также проверить базу данных с помощью портала управления Azure или обозревателя объектов SQL Server в Visual Studio. В следующих двух действиях будет использоваться портал управления Azure для просмотра изменений в базе данных.

4. На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![][17]

5. На портале управления выполните запрос для просмотра изменений, внесенных приложением Магазина Windows. Ваш запрос будет аналогичен показанному ниже, но вместо `todolist` укажите имя вашей базы данных.

        SELECT * FROM [todolist].[todoitems]	

    ![][18]

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для магазина Windows возможностей работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов c разбиением по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с проверкой подлинности]
  <br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник по принципам использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.
  
<!-- Anchors. -->

[Загрузка проекта приложения Магазина Windows]: #download-app
[Создание мобильной службы]: #create-service
[Локальная загрузка мобильной службы]: #download-the-service-locally
[Обновление приложения Магазина Windows для использования мобильной службы]: #update-app
[Тестирование приложения Магазина Windows для службы, размещенной локально]: #test-locally-hosted
[Публикация мобильной службы в Azure]: #publish-mobile-service
[Тестирование приложения Магазина Windows с помощью службы, размещенной в Azure]: #test-azure-hosted
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png

[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-ite
	ms.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-js
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/


[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Справочник по принципам использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library/
[Класс MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030


<!--HONumber=42-->
