<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Начало работы с данными - Android" pageTitle="Начало работы с данными (Android) | Центр разработчиков для мобильных устройств" metaKeywords="данные android Azure, данные мобильных служб Azure, windows droid, windows android, microsoft droid, microsoft android" description="Узнайте, как начать работу с мобильными службами для использования данных в приложении Android." metaCanonical="" services="" documentationCenter="Mobile" title="Начало работы с данными в мобильных службах" authors="ricksal,glenga" solutions="" manager="" editor="" />



# Приступая к работе с данными в мобильных службах
<div class="dev-center-tutorial-selector sublanding">    
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a> </div>	


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении Android. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a><span class="time">15:32</span></div>
</div>

<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет лучше понять, как с помощью мобильных служб можно использовать Azure для хранения данных и их извлечения из приложения Android. В этом разделе рассматриваются многие действия, выполняемые при быстром запуске мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-android">Приступая к работе с мобильными службами</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения Android] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Бесплатная пробная версия Azure</a>.</p></div> 

Для работы с этим учебником требуется пакет [Android SDK для мобильных служб]; пакет <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Android SDK</a>, который включает интегрированную среду разработки Eclipse и подключаемый модуль средств разработки Android (ADT); а также Android 4.2 или более поздней версии. 

<div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике приведены инструкции по установке пакета Android SDK и пакета Android SDK для мобильных служб. Для загружаемого проекта GetStartedWithData требуется Android 4.2 или более поздней версии. Однако для пакета SDK для мобильных служб требуется только Android 2.2 или более поздней версии.</p>
</div>



<h2><a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData</h2>

###Получение кода примера

Этот учебник основан на [приложении GetStartedWithData][GitHub], которое является приложением Android. Пользовательский интерфейс этого приложения совпадает с интерфейсом приложения, созданного при быстром запуске мобильных служб Android. Отличие заключается в том, что добавленные элементы хранятся локально в памяти. Будет добавлен код, необходимый для хранения данных в хранилище.


1. Загрузите пример приложения `GetStartedWithData` и распакуйте файлы на своем компьютере. 

2. В Eclipse щелкните **Файл**, затем щелкните **Импорт**, разверните элемент **Android**, щелкните **Существующий код Android в рабочую область**, а затем нажмите кнопку **Далее**. 

 	![][14]

3. Нажмите кнопку **Обзор**, перейдите в расположение развернутых файлов проекта, нажмите кнопку **ОК** и убедитесь, что отмечен проект TodoActivity. Если необходимо скопировать проект в рабочую область, установите флажок **Скопировать проекты в рабочую область**. Наконец, нажмите кнопку **Готово**. 

 	![][15]

	При этом файлы проекта импортируются в текущую рабочую область.

###Проверка версии пакета SDK для Android

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]


###Проверка и запуск примера кода

1. В обозревателе решений разверните **GetStartedWithData**, **src** и **.com.example.GetStartedWithData**, затем изучите файл ToDoActivity.java.

   	![][12]

   	Обратите внимание на примечания `//TODO`, указывающие действия, которые необходимо выполнить, чтобы приложение работало с вашей мобильной службой.

6. Для запуска проекта в меню **Запуск** щелкните **Запуск**, а затем щелкните **приложение Android**.

	<div class="dev-callout"><strong>Примечание.</strong> <p>Можно запустить этот проект с помощью Android-телефона или с помощью эмулятора Android. Чтобы работать с Android-телефоном, необходимо загрузить USB-драйвер для конкретного телефона.</p> <p>Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство на платформе Android (AVD). Для создания и управления этими устройствами используйте диспетчер AVD.</p></div>

7. В приложении введите содержательный текст, например _Завершение работы с учебником_, и нажмите кнопку **Добавить**.

   	![][13]

   	Обратите внимание, что сохраненный текст хранится в памяти и отображается в списке ниже.

<h2><a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы на портале управления</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для использования мобильной службы для доступа к данным</h2>

После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции. 

1. Если пакет [Android SDK для мобильных служб] еще не установлен, загрузите его и распакуйте сжатые файлы.

2. Скопируйте файлы `.jar` из папки `mobileservices` пакета SDK в папку `libs` проекта GetStartedWithData.

3. В обозревателе пакетов Eclipse щелкните правой кнопкой мыши папку `libs`, щелкните **Обновить**, после чего появятся скопированные JAR-файлы.

  	Это приведет к добавлению в рабочую область ссылки на пакет SDK для мобильных служб.

4. Откройте файл AndroidManifest.xml и добавьте в него следующую строку, которая позволяет приложению получить доступ к мобильным службам в Azure.

		<uses-permission android:name="android.permission.INTERNET" />

5. В обозревателе пакетов откройте файл TodoActivity.java, расположенный в пакете com.example.getstartedwithdata, и раскомментируйте следующие строки кода: 

		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
		import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
		import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
		import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

		import java.net.MalformedURLException;
 
6. Мы удалим расположенный в памяти список, используемый приложением на данный момент, а значит, сможем заменить его на мобильную службу. В классе **ToDoActivity** раскомментируйте следующую строку кода, которая определяет существующий список **toDoItemList**.

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7. Когда будет выполнен предыдущий шаг, проект сообщит об ошибках построения. Найдите три оставшихся места, где используется переменная `toDoItemList`, и раскомментируйте указанные разделы. Кроме того, удалите `import java.util.ArrayList`. Это полностью удалит список, расположенный в памяти. 

8. Теперь добавим нашу мобильную службу. Раскомментируйте следующие строки кода:

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

9. Найдите класс ProgressFilter в нижней части файла и раскомментируйте его. Для этого класса отображается индикатор "Загрузка" при выполнении сетевых операций на MobileServiceClient.


10. На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

11. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

   	![][8]

  	Эти значения потребуются при обращении к мобильной службе из кода приложения.

12. В методе **onCreate** раскомментируйте следующие строки кода, в которых определяется переменная **MobileServiceClient**:

		try {
		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
			mClient = new MobileServiceClient(
					"MobileServiceUrl",
					"AppKey", 
					this).withFilter(new ProgressFilter());

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
		} catch (MalformedURLException e) {
			createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
		}

  	Код создает экземпляр MobileServiceClient, используемый для доступа к мобильной службе. Также будет создан экземпляр MobileServiceTable, который используется для хранения данных прокси-сервера в мобильной службе.

13. В приведенном выше коде замените `MobileServiceUrl` и `AppKey` на URL-адрес мобильной службы и ключ приложения соответственно.



14. Раскомментируйте эти строки метода **checkItem**:

		mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {	
			public void onCompleted(ToDoItem entity, Exception exception,
					ServiceFilterResponse response) {
				if(exception == null){
					if (entity.isComplete()) {
						mAdapter.remove(entity);
					}
				} else {
					createAndShowDialog(exception, "Error");	
				}
			}
		});

   	В мобильную службу будет отправлено обновление элемента. Элементы, отмеченные флажками, будут удалены из адаптера.
    
15. Раскомментируйте эти строки метода **addItem**:
	
		mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {
			
			public void onCompleted(ToDoItem entity, Exception exception,
					ServiceFilterResponse response) {
				if(exception == null){
					if (!entity.isComplete()) {
						mAdapter.add(entity);
					}
				} else {
					createAndShowDialog(exception, "Error");
				}				
			}
		});
		

  	Этот код создает новый элемент и вставляет его в таблицу в удаленной мобильной службе.

16. Раскомментируйте эти строки метода **refreshItemsFromTable**:

		mToDoTable.where().field("complete").eq(false)
		.execute(new TableQueryCallback<ToDoItem>() {
		     public void onCompleted(List<ToDoItem> result, 
		    		 int count, Exception exception, 
		    		 ServiceFilterResponse response) {
						
						if(exception == null){
							mAdapter.clear();
	
							for (ToDoItem item : result) {
								mAdapter.add(item);
							}
						} else {
							createAndShowDialog(exception, "Error");
						}
					}
				}); 

	При этом будет создан запрос к мобильной службе и будут возвращены все элементы, которые не помечены как завершенные. Элементы добавляются к адаптеру для привязки.
		


<h2><a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой</h2>

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, можно протестировать работу приложения с мобильными службами, используя либо эмулятор Android, либо телефон Android.

1. В меню **Запуск** щелкните **Запуск**, чтобы запустить проект.

	Это приведет к выполнению приложения, построенного с помощью пакета Android SDK и использующего клиентскую библиотеку для отправки запроса, возвращающего элементы из вашей мобильной службы.

5. Как и ранее, введите содержательный текст, затем нажмите **Добавить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. На [портале управления] щелкните **Мобильные службы** и выберите свою мобильную службу.

4. Откройте вкладку **Данные**, а затем нажмите **Обзор**.

   	![][9]
  
   	Обратите внимание, что таблица **TodoItem** теперь содержит данные с рядом значений, созданных мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для Android.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения возможностей работы с данными из мобильных служб в приложение Android. 

Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После изучения серии учебников по работе с данными ознакомьтесь с одним из следующих учебников Android:

* [Приступая к работе с аутентификацией] 
	<br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений с использованием мобильных служб.

<!-- Anchors. -->
[Загрузка проекта приложения Android]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png


[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-android
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-android
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android

[Портал управления Azure]: https://manage.windowsazure.com/
[портале управления]: https://manage.windowsazure.com/
[Android SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

