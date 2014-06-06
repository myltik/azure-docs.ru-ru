<properties linkid="develop-mobile-tutorials-get-started-with-data-wp8" urlDisplayName="Приступая к работе с данными" pageTitle="Приступая к работе с данными (WP8) - Мобильные службы Azure | Центр разработчиков для мобильных служб" metaKeywords="" description="Сведения о том, как приступить к работе с использованием данных из мобильных служб Azure для приложения Windows Phone 8" metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с данными в мобильных службах" authors=""  solutions="" writer="glenga" manager="" editor=""/>


# Приступая к работе с данными в мобильных службах
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>	

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Серверная часть .NET">Серверная часть .NET</a> | 
	<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении Windows Phone 8. В этом учебнике вам предстоит загрузить приложение, которое хранит данные в памяти, создать мобильную службу, интегрировать эту мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">12:54</span></div>
</div>

<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет вам лучше понять, как с помощью мобильных службы можно использовать Azure для хранения и извлечения данных из приложения Windows Phone 8. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-wp8">Приступая к работе с мобильными службами</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения Windows Phone 8] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

В этом учебнике требуется пакет [Windows Phone 8 SDK], работающий в среде Windows 8. 

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником потребуется учетная запись Azure с включенной возможностью мобильных служб Azure.</p> <ul> <li>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F" target="_blank">Бесплатная пробная версия Azure</a>.</li></ul> </div> 

<h2><a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData</h2>

Этот учебник основан на приложении Windows Phone 8 [GetStartedWithData][Примеры кода для разработчиков]. Пользовательский интерфейс приложения совпадает с интерфейсом приложения, созданного в кратком руководстве по использованию мобильных служб. Отличие заключается в том, что добавленные элементы хранятся локально в памяти. 

1. Загрузите версию примера приложения GetStartedWithData с веб-сайта [Примеры кода для разработчиков]. 

2. В Visual Studio 2012 Express для Windows Phone 8 откройте загруженный проект и изучите файл MainPage.xaml.cs.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся во внутренней памяти **ObservableCollection&lt;TodoItem&gt;**.

3. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.


4. В приложении введите какой-либо текст в текстовом поле, а затем щелкните кнопку **Сохранить**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается в списке ниже.

<h2><a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы на портале управления</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для использования мобильной службы для доступа к данным</h2>

После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции. 

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управление пакетами NuGet**.

2. В левой области выберите категорию **Интернет**, выполните поиск `WindowsAzure.MobileServices`, щелкните пункт **Установить** в пакете **Мобильные службы Azure**, а затем примите условия лицензионного соглашения.

  	![][7]

  	При этом клиентская библиотека мобильных служб будет добавлена в проект.

3. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

4. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

   	![][8]

  	Эти значения потребуются при обращении к мобильной службе из кода приложения.

5. В Visual Studio откройте файл App.xaml.cs и добавьте или раскомментируйте следующие операторы `using`:

       	using Microsoft.WindowsAzure.MobileServices;

6. В том же файле раскомментируйте следующий код, который определяет переменную **MobileService**, и укажите URL-адрес и ключ приложения мобильной службы в конструкторе **MobileServiceClient** в указанном порядке.

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	Это приведет к созданию нового экземпляра **MobileServiceClient**, который используется для получения доступа к мобильной службе.

6. В файле MainPage.xaml.cs добавьте или раскомментируйте следующие операторы `using`:

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. В том же файле замените определение класса **TodoItem** следующим кодом:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

7. Закомментируйте строку, которая определяет существующую коллекцию **items**, а затем раскомментируйте следующие строки:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
			App.MobileService.GetTable<TodoItem>();

   	Этот код создает коллекцию привязок с поддержкой мобильных служб (**items**) и прокси-класс для таблицы базы данных SQL **TodoItem** (**todoTable**). 

7. В методе **InsertTodoItem** удалите строку кода, которая устанавливает значение свойства **TodoItem**.**Id**, добавьте модификатор **async** в метод и раскомментируйте следующую строку кода:

        await todoTable.InsertAsync(todoItem);

  	Этот код вставляет новый элемент в таблицу.

8. В методе **RefreshTodoItems** добавьте модификатор **async**, затем раскомментируйте следующую строку кода:

        items = await todoTable.ToCollectionAsync();

   	Это установит привязку к коллекции элементов в todoTable, которая содержит все объекты TodoItem, возвращенные из мобильной службы. 

9. В методе **UpdateCheckedTodoItem** добавьте модификатор **async**, затем раскомментируйте следующую строку кода:

         await todoTable.UpdateAsync(item);

   	В результате в мобильную службу будет отправлено обновление элемента.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

<h2><a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой</h2>

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. Как и ранее, введите текст в текстовом поле и нажмите кнопку **Сохранить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4. Откройте вкладку **Данные**, а затем щелкните **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для Windows Phone 8.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для Windows Phone 8 возможностей работы с данными в мобильных службах. Далее рассмотрите выполнение следующего учебника, который основан на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения ряда данных можно попробовать выполнить один из следующих учебников по Windows Phone 8.

* [Приступая к работе с проверкой подлинности] 
  <br/>Сведения о выполнении проверки подлинности учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений с использованием мобильных служб.
 
<!-- Anchors. -->
[Загрузка проекта приложения Windows Phone 8]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png





[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-wp8
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8

[Портал управления Azure]: https://manage.windowsazure.com/
[Портале управления]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Примеры кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=271146

