<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Приступая к работе с данными" pageTitle="Приступая к работе с данными (JavaScript) - Мобильные службы" metaKeywords="" description="Узнайте, как приступить к работе с данными с помощью мобильных служб Azure" metaCanonical="https://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012" documentationCenter="Mobile" authors="" />
# Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Магазин Windows JavaScript" class="current">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>	

В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении Магазина Windows. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

<div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике функции мобильных служб добавляются в приложение Магазина Windows, созданное в Visual Studio 2012. Visual Studio 2013 предоставляет новые возможности, которые упрощают подключение приложения Магазина Windows к мобильным службам. Дополнительные сведения см. в разделе <a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js/">Приступая к работе с данными в мобильных службах</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows][Разработка приложения для Магазина Windows] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData</h2>

Этот учебник основан на приложении Магазина Windows [GetStartedWithData][Примеры кода для разработчиков]. Пользовательский интерфейс для этого приложения совпадает с интерфейсом приложения, созданного в кратком руководстве по использованию мобильных служб. Отличие заключается в том, что добавленные элементы хранятся локально в памяти. 

1. Загрузите версию примера приложения GetStartedWithData на JavaScript с веб-сайта [Примеры кода для разработчиков]. 

   	![][10]

2. В Visual Studio 2012 Express для Windows 8 откройте загруженный проект, откройте папку **js** и просмотрите файл default.js.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся в объекте **List** в локальной памяти.

3. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4. В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запрос и обновление данных**

<h2><a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы на портале управления</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для использования мобильной службы для доступа к данным</h2>

После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции. 

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управление пакетами NuGet**.

2. В левой панели выберите категорию **Интернет**, найдите элемент `WindowsAzure.MobileServices.WinJS`, нажмите кнопку **Установить** в пакете **Мобильные службы Azure для WinJS**, а затем примите условия лицензионного соглашения. 

  	![][7]

  	При этом клиентская библиотека мобильных служб будет добавлена в проект.

2. В файле проекта default.html добавьте следующую ссылку на скрипт в заголовке страницы:

        <script type="text/javascript" src="/js/MobileServices.js"></script>

3. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

4. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес сайта**. Нажмите кнопку **Управление ключами** и запишите **ключ приложения**.

   	![][8]

  	Эти значения потребуются при обращении к мобильной службе из кода приложения.

5. В Visual Studio откройте файл default.js, раскомментируйте следующий код, который определяет переменную **client** и укажите URL-адрес и ключ приложения мобильной службы в конструкторе **MobileServiceClient** в указанном порядке.

            var client = new WindowsAzure.MobileServiceClient(
                "AppUrl",
                "appKey"
            );

  	Код создает экземпляр MobileServiceClient, используемый для доступа к мобильной службе.

6. Раскомментируйте следующую строку кода:

        var todoTable = client.getTable('TodoItem');

   	Этот код создает прокси-объект (**todoTable**) для новой таблицы базы данных. 

7. Замените функцию **InsertTodoItem** следующим кодом:

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            });
        };

  	Этот код вставляет новый элемент в таблицу.

8. Замените функцию **RefreshTodoItems** следующим кодом:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the TodoItems table. 
            todoTable.read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };      

   	Это установит привязку к коллекции элементов в таблице todoTable, которая содержит все завершенные элементы, возвращенные из мобильной службы. 

9. Замените функцию **UpdateCheckedTodoItem** следующим кодом:
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
        };

   	В результате в мобильную службу будет отправлено обновление элемента.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

<h2><a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой</h2>

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. Как и ранее, введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [портале управления] щелкните **Мобильные службы** и выберите свою мобильную службу.

4. Откройте вкладку **Данные**, а затем нажмите **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

5. В приложении отметьте один из элементов списка, а затем вернитесь на вкладку "Обзор" на портале и нажмите кнопку **Обновить**. 

  	Обратите внимание, что значение выполнения изменилось с **false** на **true**.

6. В файле проекта default.js замените существующую функцию **RefreshTodoItems** следующим кодом, который будет отфильтровывать выполненные элементы:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. В приложении отметьте другой элемент в списке, затем нажмите кнопку **Обновить**.

   	Обратите внимание, что отмеченный элемент теперь исчез из списка. Каждое обновление ведет к обмену данными с мобильной службой, которая теперь будет возвращать отфильтрованные данные.

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

<!-- Anchors. -->

[Разработка приложения для Магазина Windows]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png






[7]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
[8]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png


<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-js
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js-vs2012
[C# и XAML]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012

[Портал управления Azure]: https://manage.windowsazure.com/
[портале управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[Примеры кода для разработчиков]:  http://go.microsoft.com/fwlink/?LinkId=262308

