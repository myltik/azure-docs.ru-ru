<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Приступая к работе с данными" pageTitle="Приступая к работе с данными (магазин Windows) | Центр разработчиков для мобильных служб" metaKeywords="" description="Сведения о том, как приступить к работе с помощью мобильных служб для более эффективного использования данных в вашем приложении магазина Windows" metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с данными в мобильных службах" authors=""  solutions="" writer="glenga" manager="" editor="" />



# Приступая к работе с данными в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet" title="Магазин Windows C#" class="current">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Сервер .NET">Сервер .NET</a> | 
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a>
</div>


<p>В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении магазина Windows. В этом учебнике предстоит загрузить проект 2013 Visual Studio для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>

<div class="dev-callout"><b>Примечание.</b>
<p>Для этого учебника требуется платформа Visual Studio 2013, которая делает проще подключение приложения магазина Windows к мобильным службам. Чтобы выполнить ту же базовую процедуру с использованием Visual Studio 2012, выполните действия, описанные в разделе <a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/">Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для магазина Windows][Get the Windows Store app] 
2. [Создание мобильной службы в Visual Studio]
3. [Добавление таблицы данных для хранения и обновления приложения]
5. [Тестирование работы приложения с мобильными службами]

<div class="dev-callout"><strong>Примечание</strong>. <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Бесплатная пробная версия Azure</a></p></div> 

<h2><a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData</h2>

Этот учебник основан на проекте [Приложение GetStartedWithMobileServices][Developer Code Samples site] — проекте приложения для магазина Windows в Visual Studio 2013. Пользовательский интерфейс для этого приложения идентичен приложению, которое создается при быстром начале работы с мобильными службами, за исключением того, что добавленные элементы хранятся локально в памяти. 

1. Загрузите версию примера приложения GetStartedWithMobileServices на C# с веб-сайта [Примеры кода для разработчиков]. 

   	![][10]

2. В Visual Studio 2013 откройте загруженный проект и просмотрите файл MainPage.xaml.cs.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся в локальной памяти **ObservableCollection&lt;TodoItem&gt;**

3. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4. В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запрос и обновление данных**

<h2><a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы в Visual Studio</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7"><li><p>В обозревателе решений откройте файл с кодом App.xaml.cs и обратите внимание на новое статическое поле, добавленное в класс **App**, которое выглядит как в следующем примере:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью экземпляра <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">класса MobileServiceClient</a>. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Это статическое поле доступно для всех страниц в приложении.</p>
</li>
</ol>

<h2><a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу и обновление приложения</h2>

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

3. В файле MainPage.xaml.cs добавьте или раскомментируйте следующие операторы "using": 

		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

4. В том же файле замените определение класса TodoItem следующим кодом: 

		public class TodoItem
		{
		    public string Id { get; set; }
		
		    [JsonProperty(PropertyName = "text")]
		    public string Text { get; set; }
		
		    [JsonProperty(PropertyName = "complete")] 
		    public bool Complete { get; set; }
		}

	Указанный **JsonPropertyAttribute** используется для определения сопоставления между именами свойств в типе клиента и именами столбцов в базовой таблице данных.

5. Закомментируйте строку, которая определяет коллекцию существующих элементов, затем раскомментируйте или добавьте следующие строки и замените _&lt;yourClient&gt;_ полем `MobileServiceClient`, добавленным в файл App.xaml.cs, после подключения проекта к мобильной службе: 

		private MobileServiceCollection<TodoItem, TodoItem> items;
		private IMobileServiceTable<TodoItem> todoTable = 
		    App.<yourClient>.GetTable<TodoItem>();
		  
	Этот код создает коллекцию привязок (элементы) и прокси-класс для таблицы базы данных (todoTable), в которых включены мобильные службы. 

6. В методе **InsertTodoItem** удалите строку кода, которая устанавливает значение свойства **TodoItem.Id**, добавьте в метод модификатор **async** и раскомментируйте следующую строку кода: 

		await todoTable.InsertAsync(todoItem);


	Этот код вставляет новый элемент в таблицу. 

	<div class="dev-callout"><strong>Примечание.</strong><p>Новые таблицы создаются со столбцами "Id", "__createdAt", "__updatedAt" и "__version". Если динамическая схема включена, мобильные службы автоматически создают новые столбцы на основе JSON-объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193175.aspx">Динамическая схема</a>.</p></div>

7. В методе **RefreshTodoItems** добавьте модификатор **async**, затем раскомментируйте следующую строку кода: 

		items = await todoTable.ToCollectionAsync();

	Это установит привязку к коллекции элементов в `todoTable`, которая содержит все объекты **TodoItem**, возвращенные из мобильной службы. 

8. В методе **UpdateCheckedTodoItem** добавьте модификатор **async**, затем раскомментируйте следующую строку кода: 

		await todoTable.UpdateAsync(item);

	В результате в мобильную службу будет отправлено обновление элемента. 

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

<h2><a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой</h2>

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. Как и ранее, введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4. Откройте вкладку **Данные**, а затем щелкните **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

5. В приложении отметьте один из элементов списка, а затем вернитесь на вкладку "Обзор" на портале и нажмите кнопку **Обновить** 

  	Обратите внимание, что значение выполнения изменилось с **false** на **true**.

6. В файле проекта MainPage.xaml.cs замените существующий метод **RefreshTodoItems** следующим кодом, который будет отфильтровывать выполненные элементы:

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

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

* [Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с .NET.
  
<!-- Anchors. -->

[Загрузка проекта приложения для магазина Windows]: #download-app
[Создание мобильной службы в Visual Studio]: #create-service
[Добавление таблицы данных для хранения и обновления приложения]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Следующие шаги]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png








[9]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png


<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: ../mobile-services-windows-store-dotnet-get-started-push/
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js

[Портал управления Azure]: https://manage.windowsazure.com/
[Портале управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Класс MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

