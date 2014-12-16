<properties urlDisplayName="Get Started with Data" pageTitle="Приступая к работе с данными (WP8). Мобильные службы Azure" metaKeywords="" description="Learn how to get started using data from your Azure Mobile Services Windows Phone 8 app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />


# Добавление мобильных служб к существующему приложению

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как с помощью мобильных служб Azure эффективно использовать данные в приложении для Windows Phone 8. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon"> </span></a>Воспроизведение видео<span class="time">12:54</span></div>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения Windows Phone 8] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

Для работы с этим учебником потребуется Visual Studio 2012 Express для Windows Phone 8 и пакет [SDK для Windows Phone 8] под управлением операционной системы Windows 8. Для выполнения действий из этого учебника и создания приложения для Windows Phone 8.1 потребуется Visual Studio 2013 с обновлением 2 или более поздней версии.

>[WACOM.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F" target="_blank">Бесплатная пробная версия Azure</a>.

## <a name="download-app"></a>Загрузка пакета GetStartedWithData

Этот учебник основан на сайте [образцов кода для разработчиков приложения][GetStartedWithData app], который представляет собой проект приложения Windows Phone Silverlight 8.  

1. Загрузите пример проекта приложения GetStartedWithData с веб-сайта [образцов кода для разработчиков]. 

	>[WACOM.NOTE]Чтобы создать приложение Windows Phone Silverlght 8.1, достаточно сменить целевую ОС в загруженном проекте приложения Windows Phone Silverlight 8 на Windows Phone 8.1. Чтобы создать приложение для Магазина Windows Phone, при скачивании примера проекта приложения GetStartedWithData выберите версию [приложения для Магазина Windows Phone](http://go.microsoft.com/fwlink/p/?LinkId=397372). 

2. В Visual Studio откройте скачанный проект и просмотрите файл MainPage.xaml.cs.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся в **ObservableCollection<TodoItem>** в памяти.

3. Нажмите клавишу **F5** для повторной сборки проекта, затем запустите приложение.

4. В приложении введите любой текст в текстовом поле, а затем нажмите кнопку **Сохранить**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается в списке ниже.

<h2><a name="create-service"></a>Создание новой мобильной службы на портале управления</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Добавление новой таблицы в мобильную службу</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>Обновление приложения для использования мобильной службы для доступа к данным</h2>

После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции. 

1. В **обозревателе решений** в Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управление пакетами NuGet**.

2. В левой панели выберите категорию **Интернет**, найдите элемент "WindowsAzure.MobileServices.WinJS"`, нажмите кнопку **Установить** в пакете **Мобильные службы Azure для WinJS**, а затем примите условия лицензионного соглашения.

  	![][7]

  	При этом клиентская библиотека мобильных служб будет добавлена в проект.

3. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

4. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

   	![][8]

  	Эти значения потребуются при обращении к мобильной службе из кода приложения.

5. В среде Visual Studio откройте файл App.xaml.cs и добавьте или раскомментируйте следующие инструкции `using`:

       	using Microsoft.WindowsAzure.MobileServices;

6. В том же файле раскомментируйте следующий код, который определяет переменную **MobileService**, а затем укажите URL-адрес и ключ приложения мобильной службы в конструкторе **MobileServiceClient** в указанном порядке.

		//public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

  	Код создает экземпляр **MobileServiceClient**, используемый для доступа к мобильной службе.

6. В файле MainPage.xaml.cs добавьте или раскомментируйте следующие инструкции "using":

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

   	Этот код создает коллекцию привязок с поддержкой мобильных служб (**items**) и класс-посредник для таблицы базы данных SQL **TodoItem** (**todoTable**). 

7. В методе **InsertTodoItem** удалите строку кода, которая присваивает значение свойства **TodoItem**.**Id**, добавьте в метод модификатор **async** и раскомментируйте следующую строку кода:

        await todoTable.InsertAsync(todoItem);

  	Этот код вставляет новый элемент в таблицу.

8. В методе **RefreshTodoItems** добавьте модификатор **async**, а затем раскомментируйте следующую строку кода:

        items = await todoTable.ToCollectionAsync();

   	Это установит привязку к коллекции элементов в todoTable, которая содержит все объекты TodoItem, возвращенные из мобильной службы. 

9. В методе **UpdateCheckedTodoItem** добавьте модификатор **async**, а затем раскомментируйте следующую строку кода:

         await todoTable.UpdateAsync(item);

   	В результате в мобильную службу будет отправлено обновление элемента.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

<h2><a name="test-app"></a>Тестирование приложения новой мобильной службы</h2>

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. Как и ранее, введите текст в текстовом поле и нажмите кнопку **Сохранить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [портале управления] щелкните **Мобильные службы** и выберите свою мобильную службу.

4. Откройте вкладку **Данные**, а затем нажмите **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для Windows Phone 8.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения возможностей работы с данными в мобильных службах в приложении для Windows Phone 8. Теперь можно переходить к следующему учебнику, который основан на приложении GetStartedWithData, созданном при прохождении данного учебника:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Использование скриптов сервера в мобильных службах для проверки и изменения данных, отправленных из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Использование разбивки на страницы в запросах для управления количеством данных, обрабатываемых в одном запросе.

После завершения ряда данных можно попробовать выполнить один из следующих учебников по Windows Phone 8.

* [Приступая к работе с проверкой подлинности] 
  <br/>Проверка подлинности пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение с помощью мобильных служб.
 
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
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
