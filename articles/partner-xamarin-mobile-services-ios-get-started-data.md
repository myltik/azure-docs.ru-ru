<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-ios" urlDisplayName="Приступая к работе с данными" pageTitle="Приступая к работе с данными (Xamarin.iOS) - мобильные службы Azure" metaKeywords="Данные Azure Xamarin.iOS, данные мобильных служб Azure " description="Хранение и доступ к данным из вашего приложения Xamarin.iOS для мобильных служб Azure" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Приступая к работе с данными в мобильных службах" authors="" />

# Приступая к работе с данными в мобильных службах
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>	

В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении Xamarin.iOS. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Azure для хранения и извлечения данных из приложения Xamarin.iOS. Так в этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-xamarin-ios">Приступая к работе с мобильными службами</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения Xamarin.iOS][GitHub] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

Для этого учебника требуются [Компонент мобильных служб Azure], [XCode 5.0][Install Xcode], [Xamarin.iOS] и iOS 5.0 или более поздней версии.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div> 

<h2><a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData</h2>

Этот учебник основан на приложении [GetStartedWithData][GitHub], которое является приложением Xamarin.iOS. Пользовательский интерфейс приложения совпадает с интерфейсом приложения, созданного кратким руководством по мобильным службам Xamarin.iOS. Отличие заключается в том, что добавленные элементы хранятся локально в памяти. 

1. Загрузите [приложение GetStartedWithData][GitHub]. 

2. В Xamarin Studio откройте загруженный проект и просмотрите класс **TodoService**.

   	Обратите внимание на несколько примечаний **/ / TODO::**, указывающих на действия, которые необходимо выполнить, чтобы приложение работало с вашей мобильной службой.

3. Перейдите в меню **Запуск** и выберите **Запуск без отладки**, чтобы запустить приложение.

4. В приложении введите какой-либо текст в текстовом поле, а затем щелкните кнопку **+**.

   	![][0]  

   	Обратите внимание, что сохраненный текст отображается в списке ниже.

<h2><a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы на портале управления</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу</h2>

Чтобы иметь возможность сохранять данные приложения в новой мобильной службе, необходимо сначала создать новую таблицу в соответствующем экземпляре базы данных SQL.

1. На портале управления щелкните **Мобильные службы**, а затем выберите только что созданную мобильную службу.

2. Щелкните вкладку **Данные**, а затем нажмите **+Создать**.

   	![][5]

   	Откроется диалоговое окно **Создание новой таблицы**.

3. В поле **Имя таблицы** введите _TodoItem_, а затем нажмите кнопку "Проверить".

  	![][6]

  	Будет создана новая таблица устройств хранения данных **TodoItem** с набором разрешений по умолчанию; это означает, что любой пользователь приложения сможет получить доступ к данным в таблице и менять их. 

    <div class="dev-callout"> 
	<b>Примечание</b> 
	<p>То же имя таблицы используется при быстром запуске мобильных служб. Однако каждая таблица создается в схеме, которая относится к данной мобильной службе. Это необходимо для предотвращения конфликтов данных, когда несколько мобильных служб используют одну и ту же базу данных.</p> 
	</div>

4. Щелкните новую таблицу **TodoItem** и убедитесь, что в ней нет ни одной строки данных.

5. Нажмите вкладку **Столбцы** и убедитесь, что имеется только один столбец **id**, который должен быть создан автоматически.

	  Это минимальные требования для таблицы в мобильных службах. 

    <div class="dev-callout"><b>Примечание.</b>
	<p>Если в вашей мобильной службе появится динамическая схема, новые столбцы будут создаваться автоматически при отправлении объектов JSON в мобильную службу при операции вставки или обновления.</p>
    </div>

Теперь вы готовы использовать новую мобильную службу как хранилище данных для приложения.

<h2><a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для использования мобильной службы для доступа к данным</h2>

После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции. 

1. Если **Мобильные службы Azure** отсутствуют в папке "Компоненты", щелкните правой кнопкой **Компоненты**, выберите **Получить больше компонентов**, а затем выполните поиск по фразе **Мобильные службы Azure**.

2. В представлении решения в Xamarin Studio откройте класс **TodoService** и раскомментируйте следующий оператор `using`:

        using Microsoft.WindowsAzure.MobileServices;
               
3. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

4. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес сайта**. Нажмите кнопку **Управление ключами** и запишите **ключ приложения**.

   	![][8]

5. В классе **Константы** раскомментируйте следующие константы:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6. Замените **AppUrl** и **AppKey** в вышеуказанных константах на значения, указанные выше на портале управления.

7. В классе **TodoService** раскомментируйте следующую строку кода:

        private MobileServiceClient client;

   	При этом создается свойство, которое представляет MobileServiceClient, который подключается к службе

8. В классе **TodoService** раскомментируйте следующую строку кода:

        private IMobileServiceTable<TodoItem> todoTable;  

   	Это создает представление свойств таблицы мобильных служб.

9. Раскомментируйте следующие строки в конструкторе **TodoService**:

		// TODO:: Uncomment these lines to use Mobile Services
		client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
		todoTable = client.GetTable<TodoItem>(); 

    Это создает экземпляр клиента мобильной службы и создает экземпляр таблицы TodoItem.

10. Раскомментируйте следующие строки в методе **RefreshDataAsync** в **TodoService**

		// TODO:: Uncomment these lines to use Mobile Services
    try 
        {
			// This code refreshes the entries in the list view by querying the TodoItems table.
			// The query excludes completed TodoItems
			Items = await todoTable
				.Where (todoItem => todoItem.Complete == false).ToListAsync();
		} 
        catch (MobileServiceInvalidOperationException e) 
        {
			Console.Error.WriteLine (@"ERROR {0}", e.Message);
			return null;
		}
        
    При этом создается запрос для возврата всех задач, которые еще не были завершены.

11. Найдите метод **InsertTodoItemAsync** и раскомментируйте следующую строку:

		await todoTable.InsertAsync(todoItem);
		
    Этот код отправляет в мобильную службу запрос на вставку.

13. Найдите метод **CompleteItemAsync** и раскомментируйте следующую строку:

		await todoTable.UpdateAsync(item);
		
   	Этот код удаляет TodoItems, после того как они будут помечены как завершенные. 

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

<h2><a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой</h2>

1. В Xamarin Studio выберите эмулятор или устройство для развертывания в одном из первичных полей, а затем перейдите в меню **Запуск** и выберите команду **Запуск без отладки** для запуска приложения.

   	При этом будет выполнен клиент мобильных служб Azure, созданный с использованием Xamarin.iOS, который запрашивает элементы из вашей мобильной службы.

2. Как и ранее, введите текст в текстовом поле и нажмите кнопку **+**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [портале управления] щелкните **Мобильные службы** и выберите свою мобильную службу.

4. Откройте вкладку **Данные**, а затем нажмите **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для Xamarin.iOS.

## Получение полного примера
Загрузите [полный пример проекта]. Не забудьте обновить переменные **applicationURL** и **applicationKey** с использованием своих параметров Azure. 

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в iOS-приложении возможностей работы с данными в мобильных службах. 

Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников iOS:

* [Приступая к работе с аутентификацией] 
	<br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений с использованием мобильных служб.

<!-- Anchors. -->

[Разработка приложения для Магазина Windows]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Следующие шаги]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[портале управления]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Установить Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784
[Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/

[Полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331302

