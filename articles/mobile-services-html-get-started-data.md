<properties linkid="develop-mobile-tutorials-get-started-with-data-html" urlDisplayName="Приступая к работе с данными (HTML5)" pageTitle="Приступая к работе с данными (HTML5) | Центр разработчиков для мобильных служб" metaKeywords="" description="Сведения о том, как приступить к работе с помощью мобильных служб для более эффективного использования данных в вашем HTML-приложении." metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с данными в мобильных службах" authors="glenga" solutions="" manager="" editor="" />




# Приступая к работе с данными в мобильных службах
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-html" title="HTML" class="current">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a> 
</div>	


<p>В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в HTML-приложении. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>


<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет вам лучше понять, как с помощью мобильных службы можно использовать Azure для хранения и извлечения данных из HTML-приложения. Так в этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-html">Приступая к работе с мобильными службами</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта HTML-приложения]
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div> 

###Дополнительные требования

Приложение GetStartedWithData можно разместить на любом веб-сервере. Тем не менее, для вашего удобства предоставлены скрипты, которые позволяют запускать приложение по адресу `http://localhost: 8000`.
 
+ Для использования localhost в этом учебнике требуется наличие одного из следующих веб-серверов, запущенных на локальном компьютере:

	+  **В Windows**: IIS Express. IIS Express устанавливается [программой установки веб-платформы Майкрософт].   
	+  **В MacOS X**: Python, который уже должен быть установлен.
	+  **В системе Linux**: Python. Необходимо установить [последнюю версию Python]. 
	
	Можно использовать любой веб-сервер для размещения приложения, однако ниже приведены веб-серверы, которые поддерживаются доступными для загрузки сценариями.  

+ Веб-браузер, который поддерживает HTML5.

<h2><a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData</h2>

Этот учебник основан на HTML5-[приложении GetStartedWithData]. Пользовательский интерфейс приложения совпадает с интерфейсом приложения, созданного кратким руководством по мобильным службам. Отличие заключается в том, что добавленные элементы хранятся локально в памяти. 

1. [Загрузите файлы проекта HTML-приложения][приложении GetStartedWithData].

2. В HTML-редакторе откройте загруженный проект и просмотрите файл app.js.

   	Обратите внимание, что элементы хранятся в памяти в объекте **Array** (**staticItems**). Обновите страницу, после чего данные исчезнут. Они не сохраняются.

3. Запустите один из следующих командных файлов во вложенной папке **server**.

	+ **launch-windows** (Компьютеры Windows) 
	+ **launch-mac.command** (Компьютеры Mac OS X)
	+ **launch-linux.sh** (Компьютеры Linux)

	<div class="dev-callout"><b>Примечание.</b>
		<p>На компьютере Windows нажмите клавишу `R`, когда в PowerShell появится запрос на запуск скрипта. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку сценария.</p>
	</div>
	
	Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено новое приложение.

4. Откройте URL-адрес <a href="http://localhost:8000/" target="_blank">http://localhost: 8000/</a> в веб-браузере, чтобы запустить приложение.

5. В разделе **Введите новую задачу** введите содержательный текст в приложении, например _Complete the tutorial_, и нажмите кнопку **Добавить**.

   	![][0]  

   	Обратите внимание, что сохраненный текст добавляется в массив **staticItems**, а страница обновляется для отображения нового элемента.

<h2><a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы на портале управления</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу</h2>

Чтобы иметь возможность сохранять данные приложения в новой мобильной службе, необходимо сначала создать новую таблицу в соответствующем экземпляре базы данных SQL.

1. На портале управления щелкните **Мобильные службы**, а затем выберите только что созданную мобильную службу.

2. Щелкните вкладку **Данные**, а затем нажмите **+Создать**.

   	![][5]

   	Откроется диалоговое окно **Создание новой таблицы**.

3. В поле **Имя таблицы** введите __TodoItem_, а затем нажмите кнопку "Проверить".

  	![][6]

  	Это создает новую таблицу хранения данных **TodoItem** с установленными по умолчанию разрешениями. Это означает, что любой пользователь, имеющий ключ приложения, который входит в состав приложения, имеет доступ и может изменять данные в таблице.

    <div class="dev-callout"> 
	<b>Примечание</b> 
	<p>То же имя таблицы используется при быстром запуске мобильных служб. Однако каждая таблица создается в схеме, которая относится к данной мобильной службе. Это необходимо для предотвращения конфликтов данных, когда несколько мобильных служб используют одну и ту же базу данных.</p> 
	</div>

4. Щелкните новую таблицу **TodoItem** и убедитесь, что в ней нет ни одной строки данных.

5. Щелкните вкладку **Столбцы**. Убедитесь, что следующие столбцы по умолчанию автоматически создаются: 
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>Имя столбца</th>
 	<th>Тип</th>
 	<th>Индекс</th>
 	</tr>
 	<tr>
 	<td>id</td>
 	<td>string</td>
 	<td>Индексировано</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>date</td>
 	<td>Индексировано</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>date</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>timestamp (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 

  	Это минимальные требования для таблицы в мобильных службах. 

    <div class="dev-callout"><b>Примечание.</b>
	<p>Если в вашей мобильной службе появится динамическая схема, новые столбцы будут создаваться автоматически при отправлении объектов JSON в мобильную службу при операции вставки или обновления.</p>
    </div>

6. На вкладке **Настройка** убедитесь, что в списке **Разрешить запросы от имен узлов** в разделе **Общий доступ к ресурсам независимо от источника (CORS)** уже имеется значение `localhost`. В противном случае введите `localhost` в поле **Имя узла**, затем нажмите кнопку **Сохранить**.

  	![][11]

	<div class="dev-callout"><b>Примечание.</b>
		<p>Если развернуть приложение quickstart на веб-сервере, отличном от localhost, необходимо добавить имя узла веб-сервера в список <strong>Разрешить запросы имен узлов</strong>. Дополнительные сведения см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn155871.aspx" target="_blank">Общий доступ к ресурсам независимо от источника</a>.</p>
	</div>

Теперь вы готовы использовать новую мобильную службу как хранилище данных для приложения.

<h2><a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для использования мобильной службы для доступа к данным</h2>

После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции. 

3. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

4. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес сайта**. Нажмите кнопку **Управление ключами** и запишите **ключ приложения**.

   	![][8]

  	Эти значения потребуются при обращении к мобильной службе из кода приложения.

1. В веб-редакторе откройте файл проекта index.html и добавьте следующую строку в ссылки на скрипты на странице:

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

5. В редакторе откройте файл app.js, раскомментируйте следующий код, который определяет переменную **MobileServiceClient** и укажите URL-адрес и ключ приложения мобильной службы в конструкторе **MobileServiceClient** в указанном порядке:

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	Код создает экземпляр MobileServiceClient, используемый для доступа к мобильной службе.

6. Закомментируйте следующие строки кода:

		var itemCount = 0;
		var staticItems = [];

	Данные будут храниться в мобильной службе, а не в массиве в памяти.

6. Раскомментируйте следующую строку кода:

        todoItemTable = client.getTable('todoitem');

   	Этот код создает прокси-объект (**todoItemTable**) для базы данных SQL **TodoItem**. 

7. Замените обработчик событий **$('#add-item').submit** следующим кодом:

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems);
			}
			textbox.val('').focus();
			evt.preventDefault();
		});


  	Этот код вставляет новый элемент в таблицу.

8. Замените метод **refreshTodoItems** следующим кодом:

		function refreshTodoItems() {

			var query = todoItemTable;

			query.read().then(function(todoItems) {
				listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text)));
				});
					   
				$('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
				$('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
			});
		}
	   

   Этот код отправляет запрос мобильной службе, который возвращает все элементы. Выполняется итерация по результатам, а данные отображаются на странице. 

9. Замените обработчики событий **$(document.body).on('change', '.item-text')** и **$(document.body).on('change', '.item-complete')** следующим кодом:
        
		$(document.body).on('change', '.item-text', function() {
			var newText = $(this).val();
			todoItemTable.update({ id: getTodoItemId(this), text: newText });
		});

		$(document.body).on('change', '.item-complete', function() {
			var isComplete = $(this).prop('checked');
			todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
				.then(refreshTodoItems);
		});
 
   	Этот код отправляет обновление элемента мобильной службе при изменении текста или установке флажка.

10. Замените обработчик событий **$(document.body).on('click', '.item-delete')** следующим кодом:

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	Этот код отправляет мобильной службе запрос на удаление службы при нажатии кнопки **Delete**.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

<h2><a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой</h2>

4. Повторно откройте URL-адрес <a href="http://localhost:8000/" target="_blank">http://localhost: 8000/</a> в веб-браузере, чтобы запустить приложение.

    <div class="dev-callout"><b>Примечание.</b>
	<p>Если требуется перезапустить веб-сервера, повторите действия, описанные в первом разделе.</p>
    </div>

2. Как и ранее, введите текст в поле **Введите новую задачу** и нажмите кнопку **Добавить**. 

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [портале управления] щелкните **Мобильные службы** и выберите свою мобильную службу.

4. Откройте вкладку **Данные**, а затем нажмите **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

5. В приложении отметьте один из элементов списка, а затем вернитесь на вкладку "Обзор" на портале и нажмите кнопку **Обновить**. 

  	Обратите внимание, что значение выполнения изменилось с **false** на **true**.

6. Найдите в файле проекта app.js метод **RefreshTodoItems** и замените строку кода, которая определяет переменную `query`, на следующую строку:

   		var query = todoItemTable.where({ complete: false });

7. Снова загрузите страницу и проверьте другой элемент в списке.

   	Обратите внимание, что отмеченный элемент теперь исчез из списка. Каждое обновление ведет к обмену данными с мобильной службой, которая теперь будет возвращать отфильтрованные данные.

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в HTML-приложении возможностей работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.
 
После ознакомления с работой с данными, узнайте, как проверять подлинность пользователей приложения, изучив один из следующих учебников и статью [Приступая к работе с проверкой подлинности].

<!-- Anchors. -->
[Загрузка проекта HTML-приложения]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png




[5]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
[6]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png

[8]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png

[11]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png

<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-html
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-html

[Портал управления Azure]: https://manage.windowsazure.com/
[портале управления]: https://manage.windowsazure.com/
[приложении GetStartedWithData]:  http://go.microsoft.com/fwlink/?LinkID=286345

[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client

[Общий доступ к ресурсам независимо от источника]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn155871.aspx


