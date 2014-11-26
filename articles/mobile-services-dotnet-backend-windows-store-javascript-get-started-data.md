<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Приступая к работе с данными в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Сервер .NET" class="current">Сервер .NET</a> | 
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="Сервер JavaScript">Сервер JavaScript</a>
</div>

В этом разделе показано, как использовать мобильные службы Azure в качестве серверного источника данных в приложении для Магазина Windows. В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для приложения, хранящего данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

Мобильная служба, которую вы создадите в этом учебнике — это серверная мобильная служба .NET. Серверная часть .NET позволяет использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе, а запускать и отлаживать ее можно на локальном компьютере. Информацию о создании мобильной службы, которая позволяет создавать в JavaScript серверную бизнес-логику, см. в соответствующем подразделе этого раздела о серверной части JavaScript.

> [WACOM.NOTE]В этом разделе также показывается, как добавить мобильные службы Azure в проект для Магазина Windows. Вы можете использовать инструменты Visual Studio 2013, чтобы добавить ту же серверную мобильную для .NET в проект универсального приложения для Windows. Дополнительную информацию см. в разделе [Универсальная версия приложения для Windows][Универсальная версия приложения для Windows] этого учебника.

В этом учебнике рассматриваются следующие основные действия:

1.  [Загрузка проекта приложения для Магазина Windows][Загрузка проекта приложения для Магазина Windows]
2.  [Создание мобильной службы][Создание мобильной службы]
3.  [Локальная загрузка мобильной службы][Локальная загрузка мобильной службы]
4.  [Обновление приложения Магазина Windows для использования мобильной службы][Обновление приложения Магазина Windows для использования мобильной службы]
5.  [Тестирование приложения Магазина Windows для службы, размещенной локально][Тестирование приложения Магазина Windows для службы, размещенной локально]
6.  [Публикация мобильной службы в Azure][Публикация мобильной службы в Azure]
7.  [Тестирование приложения Магазина Windows для службы, размещенной в Azure][Тестирование приложения Магазина Windows для службы, размещенной в Azure]

Для работы с этим учебником требуется:

-   Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]. Доступна бесплатная пробная версия.

## <a name="download-app"></a>Скачивание проекта GetStartedWithData

Этот учебник основан на [приложении GetStartedWithMobileServices][приложении GetStartedWithMobileServices] — проекте приложения для Магазина Windows в Visual Studio 2013. Пользовательский интерфейс для этого приложения аналогичен интерфейсу приложения, созданного в кратком руководстве по использованию мобильных служб. Отличие заключается в том, что добавленные элементы хранятся локально в памяти.

1.  Скачайте версию приложения GetStartedWithMobileServices для JavaScript с веб-сайта[примеров кода для разработчиков][приложении GetStartedWithMobileServices].

2.  Запустите Visual Studio 2013 с привилегиями администратора. Для этого щелкните правой кнопкой мыши Visual Studio и выберите **Запуск от имени администратора**.

3.  В Visual Studio 2013 откройте загруженный проект. В обозревателе решений разверните папку js и изучите файл default.js.

    Заметьте, что добавленные объекты **TodoItem** хранятся во внутренней памяти в `WinJS.Binding.List`.

4.  Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

5.  В приложении введите какой-либо текст в расположенном ниже поле **Insert a TodoItem** (Вставка TodoItem), затем щелкните **Сохранить**.

    ![][0]

    Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запрос и обновление данных**

## <a name="create-service"></a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>Скачивание проекта мобильной службы и его добавление к решению

1.  На [портале управления Azure][портале управления Azure] щелкните новую мобильную службу или вкладку с ее облачным значком, чтобы перейти на страницу обзора.

    ![][1]

2.  Щелкните платформу **Магазин Windows**. В разделе **Начало работы** разверните элемент **Подключение существующего приложения Магазина Windows**, а затем щелкните **Загрузить**, чтобы загрузить персональный начальный проект для мобильной службы.

    ![][2]

3.  Прокрутите вниз раздел **Начало работы** до шага **Публикация службы в облаке**. Щелкните ссылку, показанную на представленном ниже снимке экрана, чтобы загрузить файл профиля публикации для только что загруженной мобильной службы.

    > [WACOM.NOTE] Сохраните файл в безопасном месте, поскольку он содержит конфиденциальные сведения, относящиеся к вашей учетной записи Azure. Вы удалите этот файл после публикации мобильной службы позднее в этом учебнике.

    ![][3]

4.  Распакуйте скачанный персонализированный начальный проект службы. Скопируйте папки, находившиеся в ZIP-файле, в один каталог **JavaScript**, в котором находится файл решения для начала работы с данными (SLN-файл). Так диспетчеру пакетов NuGet будет проще обеспечивать синхронизацию всех пакетов.

    ![][4]

5.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши свое решение для начала работы с приложением обработки данных для Магазина Windows. Щелкните **Добавить**, затем щелкните **Существующий проект**.

    ![][5]

6.  В диалоговом окне «Добавление существующего проекта» перейдите к папке проекта мобильной службы, которую вы перенесли в каталог **JavaScript**. В подкаталоге службы выберите файл проекта C# (с расширением .csproj). Щелкните **Открыть**, чтобы добавить проект в решение.

    ![][6]

7.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши только что добавленный проект службы и выберите **Сборка**, чтобы убедиться, что сборка выполняется без ошибок. Во время сборки диспетчеру пакетов NuGet может потребоваться восстановить некоторые пакеты NuGet, на которые имеются ссылки в проекте.

    ![][7]

8.  Еще раз щелкните правой кнопкой мыши проект службы. На этот раз выберите команду **Запустить новый экземпляр** в контекстном меню **Отладка**.

    ![][8]

    В Visual Studio открывается веб-страница по умолчанию для вашей службы. Щелкните **Попробовать сейчас**, чтобы с веб-страницы по умолчанию протестировать методы в мобильной службе.

    ![][9]

    Visual Studio по умолчанию размещает мобильную службу локально в IIS Express. Чтобы увидеть это, щелкните правой кнопкой мыши значок для IIS Express на панели задач.

    ![][10]

# <a name="update-app"></a>Изменение приложения для Магазина Windows для использования мобильной службы

В этом разделе вы обновите приложение Магазина Windows, чтобы использовать мобильную службу в качестве серверной службы для приложения.

1.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект приложения для Магазина Windows и выберите **Управление пакетами NuGet**.

    ![][11]

2.  В диалоговом окне «Управление пакетами NuGet» выполните поиск **WindowsAzure.MobileServices.WinJS** в коллекции пакетов в Интернете и щелкните пакет Nuget для мобильных служб Azure, чтобы установить его. Затем закройте диалоговое окно.

    ![][12]

3.  Вернитесь на страницу обзора мобильной службы на портале управления Azure и найдите действие **Подключение приложения и хранение данных в службе**. Выберите **JavaScript** в качестве языка и скопируйте фрагмент кода для создания объекта `MobileServiceClient`.

    ![][13]

4.  В обозревателе решений в Visual Studio разверните папку **js** и откройте файл default.js. Вставьте скопированный фрагмент кода в обработчик событий `app.onactivated` прямо перед определением для переменной `todoItems`. Заметьте, что фрагмент кода включает закомментированный конструктор для подключения к мобильной службе в Azure с использованием ключа приложения. Он будет раскомментирован в дальнейшем.

    ![][14]

5.  В файле default.js замените оставшийся код для обработчика событий `app.onactiviated` приведенным ниже кодом, определяющим переменную `todoItems` и операции, которые мы будем тестировать с мобильной службой. Затем сохраните файл.

    Этот код использует пакет Mobile Services SDK для JavaScript, чтобы приложение могло хранить данные в таблице, предоставленной службой, а не размещать их локально в памяти. Три основных метода: `insertTodoItem`, `refreshTodoItems` и `updateCheckedTodoItem`. Эти три метода позволяют асинхронно вставлять, запрашивать и обновлять коллекцию данных в таблице в Azure.

            var todoTable = client.getTable('TodoItem');
            var todoItems = new WinJS.Binding.List();
            var insertTodoItem = function (todoItem) {
                // This code inserts a new TodoItem into the database. 
                // Mobile Services assigns each item an id and the 
                // item is added to the Binding List
                todoTable.insert(todoItem).done(function (item) {
                    todoItems.push(item);
                });
            };
            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view 
                // by querying the TodoItems table.
                todoTable.where()
                    .read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };
            var updateCheckedTodoItem = function (todoItem) {
                // This code updates the database. 
                // When the MobileService responds, 
                // the item is removed from the list 
                todoTable.update(todoItem).done(function (item) {
                    todoItems.splice(todoItems.indexOf(item), 1);
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
            listItems.addEventListener("change", function (eventArgs) {
                var todoItem = eventArgs.target.dataContext.backingData;
                todoItem.complete = eventArgs.target.checked;
                updateCheckedTodoItem(todoItem);
            });
            refreshTodoItems();

6.  В обозревателе решений в Visual Studio откройте файл default.html. В верхней части файла добавьте новую ссылку на сценарий WinJS для MobileServices.js. Сохраните файл.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][15]

## <a name="test-locally-hosted"></a>Тестирование приложения для Магазина Windows для службы, размещенной локально

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

## <a name="publish-mobile-service"></a>Публикация мобильной службы в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Тестирование мобильной службы, опубликованной в Azure

1.  В обозревателе решений в Visual Studio разверните папку **js** и откройте файл default.js. Закомментируйте код, который создает объект `MobileServiceClient`, подключающийся к локально размещенной мобильной службе. Раскомментируйте код, который создает объект `MobileServiceClient`, подключающийся к вашей службе в Azure. Сохраните изменения в файле.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

2.  В Visual Studio нажмите клавишу F5 или щелкните **Начать отладку** в меню «Отладка». В результате перед запуском приложения для подключения к мобильной службе, размещенной удаленно в Azure, выполняется повторная сборка приложения Магазина Windows с учетом предыдущего изменения.

    ![][16]

3.  Введите новые элементы todoitem и для каждого из них щелкните **Сохранить**. Установите флажок для завершения некоторых новых элементов. Каждый новый элемент todoItem будет храниться и обновляться в базе данных SQL, ранее настроенной вами для мобильной службы на портале управления Azure.

    ![][17]

    Можно перезапустить приложение, чтобы убедиться, что изменения были сохранены в базе данных в Azure. Можно также проверить базу данных с помощью портала управления Azure или обозревателя объектов SQL Server в Visual Studio. В следующих двух действиях будет использоваться портал управления Azure для просмотра изменений в базе данных.

4.  На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![][18]

5.  На портале управления выполните запрос для просмотра изменений, внесенных приложением Магазина Windows. Ваш запрос будет аналогичен показанному ниже, но вместо `todolist` укажите имя вашей базы данных.

        SELECT * FROM [todolist].[todoitems]

    ![][19]

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для магазина Windows возможностей работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

-   [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]

    Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

-   [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]

    Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]

    Дополнительные сведения о проверке подлинности пользователей приложения.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]

    Узнайте больше о том, как использовать мобильные службы с HTML и JavaScript.



  [Универсальная версия приложения для Windows]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [Загрузка проекта приложения для Магазина Windows]: #download-app
  [Создание мобильной службы]: #create-service
  [Локальная загрузка мобильной службы]: #download-the-service-locally
  [Обновление приложения Магазина Windows для использования мобильной службы]: #update-app
  [Тестирование приложения Магазина Windows для службы, размещенной локально]: #test-locally-hosted
  [Публикация мобильной службы в Azure]: #publish-mobile-service
  [Тестирование приложения Магазина Windows для службы, размещенной в Azure]: #test-azure-hosted
  [Бесплатная пробная версия Azure]: http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [приложении GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
  [портале управления Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
  [2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
  [4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png
  [5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
  [6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
  [7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
  [8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
  [9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
  [10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png
  [11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
  [12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
  [13]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
  [14]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
  [15]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
  [16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png
  [17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
  [18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
  [19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
  [Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-js
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library/
