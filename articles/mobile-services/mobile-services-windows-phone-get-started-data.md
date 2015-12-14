<properties
	pageTitle="Добавление мобильных служб в существующее приложение (WP8) | Microsoft Azure"
	description="Узнайте, как приступить к использованию данных из приложения Windows Phone 8 для мобильных служб Azure."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="glenga"/>


# Добавление мобильных служб к существующему приложению

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Обзор

В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении для Windows Phone 8. В этом учебнике мы загрузим проект для приложения, которое хранит данные в памяти, создадим новую мобильную службу, интегрируем ее с приложением, а затем войдем на [классический портал Azure] для просмотра изменений, внесенных в данные в ходе выполнения приложения.

Ник Харрис (Nick Harris) демонстрирует порядок работы в следующем видео.
>[AZURE.VIDEO mobile-get-started-with-data-windows-phone]

##Предварительные требования

+ Visual Studio 2012 Express для Windows Phone 8 и [пакет SDK для Windows Phone 8], запущенный в среде Windows 8. Чтобы завершить этот учебник и создать приложение для Windows Phone 8.1, следует использовать Visual Studio 2013 с обновлением 2 или более позднюю версию.

+ Учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Farticles%2Fdocumentation%2Fmobile-services-windows-phone-get-started-data%2F).

##<a name="download-app"></a>Скачивание проекта GetStartedWithData

Этот учебник основан на проекте приложения для Windows Phone Silverlight 8, [GetStartedWithData app][Developer Code Samples site].

1. Скачайте пример проекта приложения GetStartedWithData с веб-сайта [примеров кода для разработчиков].

	>[AZURE.NOTE]Чтобы создать приложение для Windows Phone Silverlght 8.1, в скачанном проекте приложения для Windows Phone Silverlight 8 измените целевую ОС на Windows Phone 8.1. Чтобы создать приложение для Магазина Windows Phone, при скачивании примера проекта приложения GetStartedWithData выберите версию [приложения для Магазина Windows Phone](http://go.microsoft.com/fwlink/p/?LinkId=397372).

2. В Visual Studio откройте скачанный проект и просмотрите файл MainPage.xaml.cs.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся в объекте **ObservableCollection&lt;TodoItem&gt;** во внутренней памяти.

3. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4. В приложении введите какой-либо текст в текстовом поле, а затем щелкните кнопку **Сохранить**.

   	![][0]

   	Обратите внимание, что сохраненный текст отображается в списке ниже.

##<a name="create-service"></a>Создание мобильной службы на классическом портале Azure

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Добавление новой таблицы в мобильную службу

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Обновление приложения для доступа к данным с помощью мобильной службы

После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции.

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управлять пакетами NuGet**.

2. В левой области выберите категорию **В сети**, выполните поиск по запросу `WindowsAzure.MobileServices`, щелкните **Установить** для пакета **Мобильные службы Azure**, а затем примите условия лицензионного соглашения.

  	![][7]

  	При этом клиентская библиотека мобильных служб будет добавлена в проект.

3. На [классическом портале Azure] щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

4. Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

   	![][8]

  	Эти значения потребуются при обращении к мобильной службе из кода приложения.

5. В Visual Studio откройте файл App.xaml.cs и добавьте или раскомментируйте следующую инструкцию `using`:

       	using Microsoft.WindowsAzure.MobileServices;

6. В том же файле раскомментируйте код, который определяет переменную **MobileService**, и укажите URL-адрес и ключ приложения мобильной службы в конструкторе **MobileServiceClient** в указанном порядке.

		//public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "AppUrl",
        //    "AppKey"
        //);

  	Код создает экземпляр **MobileServiceClient**, используемый для доступа к мобильной службе.

6. В файле MainPage.cs добавьте или раскомментируйте следующие операторы `using`:

       	using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json;

7. В папке DataModel замените определение класса **TodoItem** следующим кодом:

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

7. В методе **InsertTodoItem** удалите строку кода, которая устанавливает значение свойства **TodoItem**.**Id**, добавьте в метод модификатор **async** и раскомментируйте следующую строку кода:

        await todoTable.InsertAsync(todoItem);

  	Этот код вставляет новый элемент в таблицу.

8. В методе **RefreshTodoItems** добавьте модификатор **async**, затем раскомментируйте следующую строку кода:

        items = await todoTable.ToCollectionAsync();

   	Это установит привязку к коллекции элементов в todoTable, которая содержит все объекты TodoItem, возвращенные из мобильной службы.

9. В методе **UpdateCheckedTodoItem** добавьте модификатор **async**, затем раскомментируйте следующую строку кода:

         await todoTable.UpdateAsync(item);

   	В результате в мобильную службу будет отправлено обновление элемента.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

##<a name="test-app"></a>Тестирование работы приложения с новой мобильной службой

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. Как и ранее, введите текст в текстовом поле и нажмите кнопку **Сохранить**.

   	В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3. Войдите на [классический портал Azure], выберите пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

4. Откройте вкладку **Данные**, а затем щелкните **Обзор**.

   	![][9]

   	Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для Windows Phone 8 возможностей работы с данными в мобильных службах. Рекомендуем прочитать один из следующих разделов.

* [Добавление проверки подлинности в приложение](mobile-services-windows-phone-get-started-users.md) <br/>Узнайте, как проверять подлинность пользователей приложения.

* [Добавление push-уведомлений в приложение](mobile-services-javascript-backend-windows-phone-get-started-push.md) <br/>Узнайте, как с помощью мобильных служб отправлять в приложение простейшие push-уведомления.

* [Справочник принципов использования мобильных служб C#](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Узнайте, как использовать мобильные службы с .NET.

<!-- Anchors. -->
[Download the Windows Phone 8 app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
[8]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. -->

[классический портал Azure]: https://manage.windowsazure.com/
[классическом портале Azure]: https://manage.windowsazure.com/
[пакет SDK для Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=271146
[примеров кода для разработчиков]: http://go.microsoft.com/fwlink/p/?LinkId=271146

<!---HONumber=AcomDC_1203_2015-->