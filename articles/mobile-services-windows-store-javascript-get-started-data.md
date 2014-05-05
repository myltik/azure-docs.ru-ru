<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Приступая к работе с данными" pageTitle="Приступая к работе с данными (Магазин Windows JavaScript) | Центр разработчиков для мобильных служб" metaKeywords="" description="Сведения о том, как приступить к работе с помощью мобильных служб для более эффективного использования данных в вашем приложении Магазина Windows на JavaScript" metaCanonical="https://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet/" services="" documentationCenter="Mobile" title="Приступая к работе с данными в мобильных службах" authors="glenga" solutions="" manager="" editor="" />


# Приступая к работе с данными в мобильных службах
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js" title="Магазин Windows JavaScript" class="current">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>	


<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Сервер .NET">Сервер .NET</a> | 
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a>
</div>


<p>В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении Магазина Windows. В этом учебнике предстоит загрузить проект Visual Studio 2013 для приложения, которое хранит данные в памяти, создать новые мобильные службы, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>

<div class="dev-callout"><b>Примечание.</b>
<p>Для этого учебника требуется платформа Visual Studio 2013, которая делает проще подключение приложения Магазина Windows к мобильным службам. Чтобы выполнить ту же базовую процедуру с использованием Visual Studio 2012, выполните действия, описанные в разделе <a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js-vs2012/">Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows][Get the Windows Store app] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData</h2>

Этот учебник основан на проекте [Приложение GetStartedWithMobileServices][Developer Code Samples site] — проекте приложения для Магазина Windows в Visual Studio 2013. Пользовательский интерфейс для этого приложения идентичен приложению, которое создается при быстром начале работы с мобильными службами, за исключением того, что добавленные элементы хранятся локально в памяти.  

1. Загрузите версию примера приложения GetStartedWithData на JavaScript с веб-сайта [Примеры кода для разработчиков]. 

   	![][10]

2. В Visual Studio 2012 Express для Windows 8 откройте загруженный проект, откройте папку **js** и просмотрите файл default.js.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся в объекте **List** в локальной памяти.

3. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4. В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запрос и обновление данных**

<h2><a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы в Visual Studio</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>В обозревателе решений разверните узел **службы**, **мобильные службы**, папки **&lt;вашей_службы&gt;**, откройте файл скрипта service.js и обратите внимание на новую глобальную переменную, которая выглядит, как в следующем примере:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью глобальной переменной. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Так как ссылка на этот скрипт была добавлена в файл default.html, эта переменная доступна для всех файлов скриптов, на которые также указывает ссылка на этой странице.</p>
</li>
</ol>

<h2><a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу и обновление приложения</h2>

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

6. В файле default.js закомментируйте строку, которая определяет коллекцию существующих элементов, затем раскомментируйте или добавьте следующую строку кода и замените `<yourClient>;` на переменную, добавленную в файл services.js, после подключения проекта к мобильной службе:

		var todoTable = <yourClient>.getTable('TodoItem');

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

	<div class="dev-callout"><strong>Примечание.</strong><p>Новые таблицы создаются только со столбцом Id. Если динамическая схема включена, мобильные службы автоматически создают новые столбцы на основе JSON-объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193175.aspx">Динамическая схема</a>.</p></div>

8. Замените функцию **RefreshTodoItems** следующим кодом:

		var refreshTodoItems = function () {
		    // This code refreshes the entries in the list by querying the table. 
		    todoTable.read().done(function (results) {
		        todoItems = new WinJS.Binding.List(results);
		        listItems.winControl.itemDataSource = todoItems.dataSource;
		    });
		};

   	Это установит привязку к коллекции элементов в таблицу todoTable, которая содержит все объекты **TodoItem**, возвращенные из мобильной службы. 

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

В этом учебнике показаны основы включения в приложении для Магазина Windows возможностей работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с аутентификацией]
  <br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник принципов использования мобильных служб HTML/JavaScript]
  <br/>Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.

<!-- Anchors. -->

[Разработка приложения для Магазина Windows]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Следующие шаги]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png








[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-js
[Приступая к работе с мобильными службами]: ./mobile-services-get-started.md
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[C# и XAML]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/

