<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Приступая к работе с данными" pageTitle="Приступая к работе с данными (Магазин Windows) | Центр разработчиков для мобильных служб" metaKeywords="" description="Узнайте, как начать применять мобильные службы для более эффективного использования данных в приложении Магазина Windows" metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с данными в мобильных службах" authors="wesmc" solutions="" manager="" editor="" />



# Приступая к работе с данными в мобильных службах


<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Магазин Windows — C#">Магазин Windows — C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Магазин Windows — JavaScript" class="current">Магазин Windows — JavaScript</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Серверная версия .NET" class="current">Серверная версия .NET</a> | 
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/"  title="Серверная версия JavaScript">Серверная версия JavaScript</a>
</div>

В этом разделе показано, как использовать мобильные службы Azure в качестве серверного источника данных для приложения Магазина Windows. В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для приложения, хранящего данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

Создаваемая в этом учебнике мобильная служба будет поддерживать среду выполнения .NET в компоненте мобильных служб. Это позволит использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.

<div class="dev-callout"><b>Примечание.</b>
<p>Для работы с этим учебником требуется Visual Studio 2013.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows]
2. [Создание мобильной службы]
3. [Локальная загрузка мобильной службы]
4. [Обновление приложения Магазина Windows для использования мобильной службы]
5. [Тестирование приложения Магазина Windows для службы, размещенной локально]
6. [Публикация мобильной службы в Azure]
7. [Тестирование приложения Магазина Windows для службы, размещенной в Azure]


<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData</h2>

Этот учебник основан на [приложении GetStartedWithMobileServices][Developer Code Samples site] — проекте приложения для Магазина Windows в Visual Studio 2013. Пользовательский интерфейс для этого приложения аналогичен интерфейсу приложения, созданного в кратком руководстве по использованию мобильных служб. Отличие заключается в том, что добавленные элементы хранятся локально в памяти. 

1. Загрузите версию примера приложения GetStartedWithMobileServices на JavaScript с [веб-сайта с примерами кода для разработчиков]. 

   	![][1]

2. Запустите Visual Studio 2013 с привилегиями администратора, для чего щелкните правой кнопкой мыши Visual Studio и выберите **Запуск от имени администратора**.

3. В Visual Studio 2013 откройте загруженный проект. В обозревателе решений разверните папку js и изучите файл default.js.

   	Заметьте, что добавленные объекты **TodoItem** хранятся в памяти в `WinJS.Binding.List`.

4. Нажмите клавишу **F5** для повторной сборки проекта, после чего запустите приложение.

5. В приложении введите какой-либо текст в расположенном ниже поле **Вставить в TodoItem**, затем щелкните **Сохранить**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запрос и обновление данных**

<h2><a name="create-service"></a><span class="short-header">Создание новой мобильной службы</span>Создание новой мобильной службы</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]



<h2><a name="download-the-service-locally"></a><span class="short-header">Локальная загрузка службы</span>Загрузка проекта мобильной службы и добавление ее в решение</h2>

1. На [портале управления Azure] щелкните новую мобильную службу или вкладку с ее облачным значком, чтобы перейти на страницу обзора.

    ![][2]

2. Щелкните платформу **Магазин Windows**. В разделе **Начало работы** разверните элемент **Подключение существующего приложения Магазина Windows**, а затем щелкните **Загрузить**, чтобы загрузить персональный начальный проект для мобильной службы. 

    ![][3]

3. Выполните прокрутку вниз раздела **Начало работы** до шага **Публикация службы в облаке**. Щелкните ссылку, показанную на представленном ниже снимке экрана, чтобы загрузить файл профиля публикации для только что загруженной мобильной службы.

    > [WACOM.NOTE] Сохраните файл в безопасном месте, поскольку он содержит важные сведения, относящиеся к вашей учетной записи Azure. Вы удалите этот файл после публикации мобильной службы позднее в этом учебнике. 

    ![][5]


4. Распакуйте загруженный персональный начальный проект службы. Скопируйте папки, находившиеся в ZIP-файле, в один каталог **JavaScript**, в котором находится файл решения для начала работы с данными (SLN-файл). Так диспетчеру пакетов NuGet будет проще обеспечивать синхронизацию всех пакетов. 

    ![][26]

5. В обозревателе решений Visual Studio щелкните правой кнопкой мыши ваше решение для начала работы с данными приложения Магазина Windows. Щелкните **Добавить**, затем щелкните **Существующий проект**.

    ![][4]

6. В диалоговом окне "Добавление существующего проекта" перейдите к папке проекта мобильной службы, которую вы перенесли в каталог **JavaScript**. В подкаталоге службы выберите файл проекта C# (с расширением .csproj). Щелкните **Открыть**, чтобы добавить проект в решение.

    ![][6]

7. В обозревателе решений Visual Studio щелкните правой кнопкой мыши только что добавленный проект службы и выберите **Сборка**, чтобы убедиться, что сборка выполняется без ошибок. Во время сборки диспетчеру пакетов NuGet может потребоваться восстановить некоторые пакеты NuGet, на которые имеются ссылки в проекте.

    ![][20]

8. Еще раз щелкните правой кнопкой мыши проект службы. На этот раз выберите команду **Запустить новый экземпляр** в контекстном меню **Отладка**.

    ![][21]

    В Visual Studio открывается веб-страница по умолчанию для вашей службы. Щелкните **Попробовать сейчас**, чтобы с веб-страницы по умолчанию протестировать методы в мобильной службе.

    ![][22]

    Visual Studio по умолчанию размещает мобильную службу локально в IIS Express. Чтобы увидеть это, щелкните правой кнопкой мыши значок для IIS Express на панели задач.

    ![][23]


<h2><a name="update-app"></a><span class="short-header">Обновление приложения Магазина Windows</span>Обновление приложения Магазина Windows для использования мобильной службы</h2>

В этом разделе вы обновите приложение Магазина Windows, чтобы использовать мобильную службу в качестве серверной службы для приложения.


1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект приложения Магазина Windows и выберите **Управление пакетами NuGet**.

    ![][7]

2. В диалоговом окне "Управление пакетами NuGet" выполните поиск **WindowsAzure.MobileServices.WinJS** в коллекции сетевых пакетов и щелкните, чтобы установить Nuget-пакет мобильных служб Azure. Затем закройте диалоговое окно.

    ![][8]

3. Вернитесь на страницу обзора мобильной службы на портале управления Azure и найдите действие **Подключение приложения и хранение данных в службе**. Щелкните **JavaScript** в качестве языка и скопируйте фрагмент кода для создания объекта `MobileServiceClient`.

    ![][9]

4. В обозревателе решений Visual Studio разверните папку **js** и откройте файл default.js. Вставьте скопированный фрагмент кода в обработчик событий `app.onactivated` прямо перед определением для переменной `todoItems`. Заметьте, что фрагмент кода включает закомментированный конструктор для подключения к мобильной службе в Azure с использованием ключа приложения. Он будет раскомментирован в дальнейшем.

    ![][10]


5. В файле default.js замените оставшийся код для обработчика событий `app.onactiviated` приведенным ниже кодом, определяющим переменную `todoItems` и операции, которые мы будем тестировать с мобильной службой. Затем сохраните файл.

    Этот код использует пакет Mobile Services SDK для JavaScript, чтобы приложение могло хранить данные в таблице, предоставленной службой, а не размещать их локально в памяти. Три основные метода: `insertTodoItem`, `refreshTodoItems` и `updateCheckedTodoItem`. Эти три метода позволяют асинхронно вставлять, запрашивать и обновлять коллекцию данных в таблице в Azure.

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

6. В обозревателе решений в Visual Studio откройте файл default.html. В верхней части файла добавьте новую ссылку на скрипт WinJS для MobileServices.js. Затем сохраните файл.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][19]



<h2><a name="test-locally-hosted"></a><span class="short-header">Локальное тестирование приложения Магазина Windows</span>Тестирование приложения Магазина Windows в службе, размещенной локально</h2>

В этом разделе вы будете использовать Visual Studio для локального размещения мобильной службы на рабочей станции разработки в IIS Express. Затем можно будет протестировать приложение и серверную службу.


1. В Visual Studio нажмите клавишу F7 или щелкните **Построить решение** в меню "Сборка", чтобы создать приложение Магазина Windows и мобильную службу. В окне вывода Visual Studio убедитесь, что оба проекта создаются без ошибок.

    ![][11]

2. В Visual Studio нажмите клавишу F5 или щелкните **Начать отладку** в меню "Отладка", чтобы запустить приложение и разместить мобильную службу локально в IIS Express. 

    ![][12]


3. Введите новый текст элемента todoitem. Нажмите кнопку **Сохранить**. При этом новый элемент todoItem вставляется в базу данных, которая создается мобильной службой, размещенной локально в IIS Express. 

    ![][13]

4. Установите флажок для одного из элементов, чтобы пометить его как завершенный.

    ![][15]

5. Чтобы в Visual Studio посмотреть изменения в базе данных, созданной для серверной службы, откройте обозреватель сервера и разверните элемент "Подключения к данным". Щелкните правой кнопкой мыши таблицу TodoItems в разделе **MS_TableConnectionString** и выберите **Показать таблицу данных**.

    ![][14]



<h2><a name="publish-mobile-service"></a><span class="short-header">Публикация мобильной службы в Azure</span>Публикация мобильной службы в Azure</h2>


[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<h2><a name="test-azure-hosted"></a><span class="short-header">Тестирование мобильной службы в Azure</span>Тестирование мобильной службы, опубликованной в Azure</h2>

1. В обозревателе решений в Visual Studio разверните папку **js** и откройте файл default.js.  Закомментируйте код, который создает объект `MobileServiceClient`, подключающийся к локально размещенной мобильной службе. Раскомментируйте код, который создает объект `MobileServiceClient`, подключающийся к вашей службе в Azure. Сохраните изменения в файле.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


2. В Visual Studio нажмите клавишу F5 или щелкните **Начать отладку** в меню "Отладка". В результате перед запуском приложения для подключения к мобильной службе, размещенной удаленно в Azure, выполняется повторная сборка приложения Магазина Windows с учетом предыдущего изменения. 

    ![][12]


3. Введите новые элементы todoitem и для каждого из них щелкните **Сохранить**. Установите флажок для завершения некоторых новых элементов. Каждый новый элемент todoItem будет храниться и обновляться в базе данных SQL, ранее настроенной вами для мобильной службы на портале управления Azure. 

    ![][16]

    Можно перезапустить приложение, чтобы убедиться, что изменения были сохранены в базе данных в Azure. Можно также проверить базу данных с помощью портала управления Azure или обозревателя объектов SQL Server в Visual Studio. В следующих двух действиях будет использоваться портал управления Azure для просмотра изменений в базе данных.

4. На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![][17]

5. На портале управления выполните запрос для просмотра изменений, внесенных приложением Магазина Windows. Ваш запрос будет похож на показанный ниже запрос, но вместо `todolist` укажите имя вашей базы данных.

        SELECT * FROM [todolist].[todoitems]

    ![][18]

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для магазина Windows возможностей работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с аутентификацией]
  <br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.
  
<!-- Anchors. -->

[Загрузка проекта приложения для Магазина Windows]: #download-app
[Создание мобильной службы]: #create-service
[Локальная загрузка мобильной службы]: #download-the-service-locally
[Обновление приложения Магазина Windows для использования мобильной службы]: #update-app
[Тестирование приложения Магазина Windows для службы, размещенной локально]: #test-locally-hosted
[Публикация мобильной службы в Azure]: #publish-mobile-service
[Тестирование приложения Магазина Windows для службы, размещенной в Azure]: #test-azure-hosted
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
[13]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
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
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-js
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js
[Серверная версия JavaScript]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js

[портале управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[веб-сайта с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/
[Класс MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

