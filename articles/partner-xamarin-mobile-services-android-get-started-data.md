<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-android" urlDisplayName="Приступая к работе с данными" pageTitle="Приступая к работе с данными (Xamarin.Android) — мобильные службы Azure" metaKeywords="Данные Azure Xamarin.Android, данные мобильных служб Azure" description="Хранение и доступ к данным из вашего приложения Xamarin.Android для мобильных служб Azure" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Приступая к работе с данными в мобильных службах" documentationCenter="Mobile" authors="" />

# Приступая к работе с данными в мобильных службах
<div class="dev-center-tutorial-selector sublanding">    
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>	

<p>В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении Xamarin.Android. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>

<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет вам лучше понять, как с помощью мобильных службы можно использовать Azure для хранения и извлечения данных из приложения Xamarin.Android. Так в этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-xamarin-android">Приступая к работе с мобильными службами</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения Xamarin.Android][GitHub] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Бесплатная пробная версия Azure</a>.</p></div> 

Для этого учебника требуются [Компонент мобильных служб Azure], [Xamarin.Android] и Android SDK 4.2 или более поздней версии. 

<div class="dev-callout"><b>Примечание.</b>
<p>Загружаемый проект GetStartedWithData требует Android 4.2 или более поздней версии. Однако для пакета Mobile Services SDK требуется только Android 2.2 или более поздней версии.</p>
</div>

<h2><a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData</h2>

Этот учебник основан на приложении [GetStartedWithData][GitHub], которое является приложением Xamarin.Android. Пользовательский интерфейс приложения совпадает с интерфейсом приложения, созданного кратким руководством по мобильным службам Android. Отличие заключается в том, что добавленные элементы хранятся локально в памяти. 

1. Загрузите пример приложения `GetStartedWithData` и распакуйте файлы на свой компьютер. 

2. В Xamarin Studio щелкните элементы **Файл** и **Открыть**, перейдите к каталогу, в который вы распаковали пример приложения GetStartedWithData, выберите **XamarinTodoQuickStart.Android.sln** и откройте его.

3. Найдите и откройте класс **TodoActivity**

   	Обратите внимание на примечания `/ / TODO::`, указывающие на действия, которые необходимо выполнить, чтобы приложение работало с вашей мобильной службой.

5. В меню **Запуск** нажмите **Запуск без отладки**; после этого вам будет предложено выбрать эмулятор или подключенное устройство USB Android.

	<div class="dev-callout"><strong>Примечание.</strong> <p>Можно запустить этот проект с помощью Android-телефона или с помощью эмулятора Android. Чтобы работать с Android-телефоном, необходимо загрузить USB-драйвер для конкретного телефона.</p> <p>Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство на платформе Android (AVD). Для создания и управления этими устройствами используйте диспетчер AVD.</p></div>

6. В приложении введите содержательный текст, например _Завершение работы с учебником_, и нажмите кнопку **Добавить**.

   	![][13]

   	Обратите внимание, что сохраненный текст хранится в памяти и отображается в списке ниже.

<h2><a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы на портале управления</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу</h2>

Чтобы иметь возможность сохранять данные приложения в новой мобильной службе, необходимо сначала создать новую таблицу.  

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

  	Это добавляет в проект компонент Mobile Services SDK.

2. Откройте файл **AndroidManifest.xml** и убедитесь в том, что существует следующая строка разрешений:

		<uses-permission android:name="android.permission.INTERNET" />

  	Это позволяет приложению получить доступ к мобильным службам в Azure.

3. В окне **Решение** откройте класс **TodoActivity** и раскомментируйте следующую строку кода: 

		using Microsoft.WindowsAzure.MobileServices;
 
4. Мы удалим список в памяти, используемый приложением на данный момент, а значит мы сможем заменить его на мобильную службу. В классе **TodoActivity** раскомментируйте следующую строку кода, которая определяет существующий список **todoItemList**.

		public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5. После выполнения предыдущего шага, проект будет указывать на ошибки построения. Найдите три оставшихся места, где используется переменная `todoItemList`, и раскомментируйте указанные разделы. 

6. Теперь добавим нашу мобильную службу. Раскомментируйте следующие строки кода:

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

8. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес сайта**. Нажмите кнопку **Управление ключами** и запишите **Ключ приложения**.

   	![][8]

  	Эти значения потребуются при обращении к мобильной службе из кода приложения.

9. В классе **Константы** раскомментируйте следующие переменные-члены:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";
        
10. Replace **AppUrl** and **AppKey** in the above variables with the values retrieved from the Management Portal above.

11. In the **OnCreate** method, uncomment the following lines of code that define the **MobileServiceClient** variable:

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
		client = new MobileServiceClient(
			Constants.ApplicationURL,
			Constants.ApplicationKey).WithFilter(filter);

		// Get the Mobile Service Table instance to use
		todoTable = client.GetTable<TodoItem>();    

  	Код создает экземпляр MobileServiceClient, используемый для доступа к мобильной службе. Также будет создан экземпляр MobileServiceTable, который используется для хранения данных прокси-сервера в мобильной службе.

12. Найдите класс ProgressFilter в нижней части файла и раскомментируйте его. Для этого класса отображается индикатор "Загрузка" при выполнении сетевых операций на MobileServiceClient.

13. Раскомментируйте эти строки метода **CheckItem**:

		try {
			await todoTable.UpdateAsync(item);
			if (item.Complete)
				adapter.Remove(item);
		} catch (Exception e) {
			CreateAndShowDialog(e, "Error");
		}

   	В мобильную службу будет отправлено обновление элемента. Элементы, отмеченные флажками, будут удалены из адаптера.
    
14. Раскомментируйте эти строки метода **AddItem**:
	
		try 
		{
			// Insert the new item
			await todoTable.InsertAsync(item);

			if (!item.Complete) 
				adapter.Add(item);			
		} 
		catch (Exception e) 
		{
			CreateAndShowDialog(e, "Error");
		}   		

  	Этот код создает новый элемент и вставляет его в таблицу в удаленной мобильной службе.

15. Раскомментируйте эти строки метода **RefreshItemsFromTableAsync**:

		try {
			// Get the items that weren't marked as completed and add them in the adapter
			var list = await todoTable.Where(item => item.Complete == false).ToListAsync ();

			adapter.Clear();

			foreach (TodoItem current in list)
				adapter.Add(current);
		} 
        catch (Exception e) 
        {
			CreateAndShowDialog(e, "Error");
		}

	При этом будет создан запрос к мобильной службе и будут возвращены все элементы, которые не помечены как завершенные. Элементы добавляются к адаптеру для привязки.
		

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

<h2><a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой</h2>

1. Для запуска проекта в меню **Запуск** щелкните **Запуск без отладки**. Будет предложено выбрать существующий образ эмулятора или подключенное устройство USB Android.

	Это выполняет приложение, построенное с помощью Xamarin.Android, которое использует клиентскую библиотеку, чтобы отправить запрос, возвращающий элементы из вашей мобильной службы.

5. Как и ранее, введите содержательный текст, затем нажмите **Добавить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [портале управления] щелкните **Мобильные службы** и выберите свою мобильную службу.

4. Откройте вкладку **Данные**, а затем нажмите **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для Xamarin.Android.

## Получение полного примера
Загрузите [полный пример проекта]. Не забудьте обновить переменные **applicationURL** и **applicationKey** с использованием своих параметров Azure. 

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении Xamarin.Android возможностей работы с данными в мобильных службах. 

Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников Xamarin.Android:

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





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png

<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-android
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Загрузка проекта приложения Android]: http://www.google.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

[Полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331302

