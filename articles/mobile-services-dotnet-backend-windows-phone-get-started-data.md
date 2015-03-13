<properties 
	pageTitle="Начало работы с данными (Windows Phone) | Центр мобильных разработок" 
	description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении Windows Phone." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="wesmc"/>

# Добавление мобильных служб к существующему приложению

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

В этом разделе показано, как добавить мобильные службы Azure в качестве серверного источника данных для приложения Windows Phone 8.1 Silverlight. В этом учебнике вам предстоит скачать проект Visual Studio для приложения, хранящего данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

Мобильная служба, создаваемая в этом учебнике, поддерживает среду выполнения .NET в мобильной службе. Это позволит использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.


В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения Windows Phone 8]
2. [Создание мобильной службы]
3. [Локальная загрузка мобильной службы]
4. [Обновление приложения Windows Phone для использования мобильной службы]
5. [Тестирование приложения Windows Phone для службы, размещенной локально]
6. [Публикация мобильной службы в Azure]
7. [Тестирование приложения Windows Phone с помощью службы, размещенной в Azure]

Для работы с данным учебником требуется следующее:

+ Visual Studio Professional 2013 с обновлением 2 или более поздней версии.
+ Учетная запись Microsoft Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div> 

##<a name="download-app"></a>Загрузка проекта GetStartedWithData

Этот учебник создан на основе [приложения GetStartedWithMobileServices][Веб-сайт с примерами кода для разработчиков], являющегося проектом приложения Windows Phone Silverlight 8.1 в Visual Studio 2013.  

1. Загрузите версию примера приложения GetStartedWithMobileServices на C# с веб-сайта [Примеры кода для разработчиков]. 

   	![][1]

	>[AZURE.NOTE]Чтобы создать приложение для Windows Phone Silverlght 8.1, в скачанном проекте приложения для Windows Phone Silverlight 8 измените целевую ОС на Windows Phone 8.1. Чтобы создать приложение для Магазина Windows Phone, при скачивании примера проекта приложения GetStartedWithData выберите [версию приложения для Магазина Windows Phone](http://go.microsoft.com/fwlink/p/?LinkId=397372).

2. Запустите Visual Studio с правами администратора, для чего щелкните правой кнопкой мыши Visual Studio и выберите пункт **Запуск от имени администратора**.

3. В Visual Studio откройте скачанный проект и просмотрите файл MainPage.xaml.cs.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся во внутренней памяти **ObservableCollection&lt;TodoItem&gt;**.

4. В Visual Studio выберите целевой объект развертывания для приложения. Можно выполнить развертывание на устройство Windows Phone или в один из эмуляторов, входящих в пакет Windows Phone SDK. В этом учебнике будет показано развертывание в эмуляторе.

    ![][19]

5. Нажмите клавишу **F5**. При этом выполняется сборка, развертывание и запуск приложения для отладки.

6. В приложении введите любой текст в текстовом поле, а затем щелкните **Сохранить**, чтобы сохранить несколько элементов в памяти.

   	![][0]  

   	Заметьте, что для каждого элемента `TodoItem` текст отображается под кнопкой обновления вместе с флажком, который позволяет помечать завершенные элементы.

<h2><a name="create-service"></a>Создание мобильной службы</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]


<h2><a name="download-the-service-locally"></a>Загрузка проекта мобильной службы и его добавление в решение</h2>

1. На [портале управления Azure] щелкните новую мобильную службу или вкладку с ее облачным значком, чтобы перейти на страницу обзора.

    ![][2]

2. Щелкните платформу **Windows Phone 8**. В разделе **Начало работы** разверните элемент **Подключение существующего приложения Windows Phone 8**, а затем щелкните **Загрузить**, чтобы загрузить персональный начальный проект для мобильной службы. 

    ![][3]

3. В том же разделе щелкните ссылку, показанную на представленном ниже снимке экрана, чтобы скачать файл профиля публикации для только что скачанной мобильной службы. 

    > [AZURE.NOTE] Сохраните файл в безопасном месте, поскольку он содержит важные сведения, относящиеся к вашей учетной записи Azure. Вы удалите этот файл после публикации мобильной службы позднее в этом учебнике. 

    ![][5]


4. Распакуйте скачанный персонализированный начальный проект службы. Скопируйте папки, находившиеся в ZIP-файле, в тот же каталог **C#**, где находится файл решения для начала работы с данными ((SLN-файл)). Так диспетчеру пакетов NuGet будет проще обеспечивать синхронизацию всех пакетов.

    ![][26]


5. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение для начала работы с приложением для обработки данных для Магазина Windows. Щелкните **Добавить**, затем щелкните **Существующий проект**.

    ![][4]

6. В диалоговом окне "Добавление существующего проекта" перейдите к папке проекта мобильной службы, которую вы перенесли в каталог **C#**. В подкаталоге службы выберите файл проекта C# (с расширением (.csproj)). Щелкните **Открыть**, чтобы добавить проект в решение.

    ![][6]

7. В обозревателе решений Visual Studio щелкните правой кнопкой мыши только что добавленный проект службы и выберите пункт **Сборка**, чтобы убедиться, что сборка выполняется без ошибок. Во время сборки диспетчеру пакетов NuGet может потребоваться восстановить некоторые пакеты NuGet, на которые имеются ссылки в проекте.

    ![][20]

8. Еще раз щелкните правой кнопкой мыши проект службы. На этот раз выберите команду **Запустить новый экземпляр** в контекстном меню **Отладка**.

    ![][21]

    В Visual Studio открывается веб-страница по умолчанию для вашей службы. Щелкните **Попробовать сейчас**, чтобы с веб-страницы по умолчанию протестировать методы в мобильной службе.

    ![][22]

    Visual Studio по умолчанию размещает мобильную службу локально в IIS Express. Чтобы увидеть это, щелкните правой кнопкой мыши значок для IIS Express на панели задач.

    ![][23]


##<a name="update-app"></a>Обновление приложения Windows Phone для использования мобильной службы

В этом разделе вы обновите приложение Windows Phone, чтобы использовать мобильную службу в качестве серверной службы для приложения.


1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект приложения Windows Phone и выберите пункт **Управление пакетами NuGet**.

    ![][7]

2. В диалоговом окне "Управление пакетами NuGet" выполните поиск **WindowsAzure.MobileServices** в коллекции сетевых пакетов и щелкните, чтобы установить Nuget-пакет мобильных служб Azure. Затем закройте диалоговое окно.

    ![][8]

3. Вернитесь на портал управления Azure и найдите действие **Подключение приложения и хранение данных в службе**. Скопируйте фрагмент кода, который создает подключение `MobileServiceClient`.

    ![][9]

4. В Visual Studio откройте файл App.xaml.cs. Вставьте фрагмент кода в начале определения класса `App`. Также добавьте следующий оператор `using` в верхнюю часть этого файла, а затем сохраните файл.

		using Microsoft.WindowsAzure.MobileServices.

    ![][10]


5. В Visual Studio откройте файл MainPage.xaml.cs и в верхней части файла добавьте следующую инструкцию using: 

		using Microsoft.WindowsAzure.MobileServices;

6. В файле MainPage.xaml.cs, открытом в Visual Studio, замените определение класса `MainPage` приведенным ниже определением, после чего сохраните файл.

    Этот код использует пакет Mobile Services SDK, чтобы приложение могло хранить данные в таблице, предоставленной службой, а не размещать их локально в памяти. Три основных метода: `InsertTodoItem`,  `RefreshTodoItems` и `UpdateCheckedTodoItem`. Эти три метода позволяют асинхронно вставлять, запрашивать и обновлять коллекцию данных в таблице в Azure. 

        public sealed partial class MainPage : PhoneApplicationPage
        {
            private MobileServiceCollection<TodoItem, TodoItem> items;
            private IMobileServiceTable<TodoItem> todoTable = 
                App.MobileService.GetTable<TodoItem>();            
            public MainPage()
            {
                this.InitializeComponent();
            }
            private async void InsertTodoItem(TodoItem todoItem)
            {
                await todoTable.InsertAsync(todoItem); 
                ite
	ms.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable 
                    .ToCollectionAsync(); 
                ListIte
	ms.ItemsSource = items;
            }
            private async void UpdateCheckedTodoItem(TodoItem item)
            {
                await todoTable.UpdateAsync(item);      
            }
            private void ButtonRefresh_Click(object sender, RoutedEventArgs e)
            {
                RefreshTodoItems();
            }
            private void ButtonSave_Click(object sender, RoutedEventArgs e)
            {
                var todoItem = new TodoItem { Text = InputText.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                item.Complete = (bool)cb.IsChecked;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }




##<a name="test-locally-hosted"></a>Тестирование приложения Windows Phone для службы, размещенной локально</h2>

В этом разделе вы научитесь использовать Visual Studio для локального тестирования приложения и мобильной службы на компьютере, где ведете разработку. Чтобы протестировать мобильную службу, размещенную локально в IIS Express, с устройства Windows Phone или одного из эмуляторов Windows Phone, необходимо настроить IIS Express и рабочую станцию таким образом, чтобы разрешить подключения к IP-адресу и порту рабочей станции. Устройства и эмуляторы Windows Phone подключаются как нелокальные сетевые клиенты.

#### Настройка IIS Express для использования удаленных подключений

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]

#### Тестирование приложения с мобильной службой в IIS Express

6. В Visual Studio откройте файл App.xaml.cs и закомментируйте определение `MobileService`, которое вы недавно вставили в файл. Добавьте новое определение для подключения на основе IP-адреса и порта, настроенных на рабочей станции. Затем сохраните файл. Код должен выглядеть следующим образом:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "http://192.168.111.11:54321");

        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "https://todolist.azure-mobile.net/",
        //    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        //);        


7. В Visual Studio нажмите клавишу F7 или щелкните **Собрать решение** в меню "Сборка", чтобы создать приложение Windows Phone и мобильную службу. В окне вывода Visual Studio убедитесь, что оба проекта создаются без ошибок.

    ![][11]

8. В Visual Studio нажмите клавишу F5 или щелкните **Начать отладку** в меню "Отладка", чтобы запустить приложение и разместить мобильную службу локально в IIS Express. 

    >[AZURE.NOTE] Запускайте Visual Studio с параметром **Запуск от имени администратора**. Иначе в IIS Express могут не загрузиться изменения applicationhost.config.

    ![][12]


9. Введите новый текст элемента todoitem. Нажмите кнопку **Сохранить**. При этом новый элемент todoItem вставляется в базу данных, которая создается мобильной службой, размещенной локально в IIS Express. Установите флажок для одного из элементов, чтобы пометить его как завершенный.

    ![][15]

10. В Visual Studio остановите отладку приложения. Чтобы посмотреть изменения в базе данных, созданной для серверной службы, откройте обозреватель сервера и разверните элемент "Подключения к данным". Щелкните правой кнопкой мыши таблицу TodoItems в разделе **MS_TableConnectionString** и выберите пункт **Показать таблицу данных**.

    ![][14]

11. После завершения тестирования с размещенной локально мобильной службой удалите созданное вами правило брандмауэра Windows, открывающее порт на вашей рабочей станции.


##<a name="publish-mobile-service"></a>Публикация мобильной службы в Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Тестирование мобильной службы, опубликованной в Azure

1. В Visual Studio откройте файл App.xaml.cs.  Закомментируйте код, который создает объект `MobileServiceClient`, подключающийся к локально размещенной мобильной службе. Раскомментируйте код, который создает объект `MobileServiceClient`, подключающийся к вашей службе в Azure. Сохраните изменения в файле.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://192.168.111.11:54321");

            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.azure-mobile.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....

2. В Visual Studio нажмите клавишу F5 или щелкните **Начать отладку** в меню "Отладка". В результате перед запуском приложения для подключения к мобильной службе, размещенной удаленно в Azure, выполняется повторная сборка приложения с учетом предыдущего изменения. 

    ![][12]

3. Введите новые элементы todoitem и для каждого из них нажмите кнопку **Сохранить**. Установите флажок для завершения некоторых новых элементов. Каждый новый элемент todoItem будет храниться и обновляться в базе данных SQL, ранее настроенной вами для мобильной службы на портале управления Azure. 

    ![][16]

    Можно перезапустить приложение, чтобы убедиться, что изменения были сохранены в базе данных в Azure. Можно также проверить базу данных с помощью портала управления Azure или обозревателя объектов SQL Server в Visual Studio. В следующих двух действиях будет использоваться портал управления Azure для просмотра изменений в базе данных.


4. На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![][17]

5. На портале управления выполните запрос для просмотра изменений, внесенных приложением. Ваш запрос будет аналогичен показанному ниже, но вместо `todolist` укажите имя вашей базы данных.

        SELECT * FROM [todolist].[todoitems]

    ![][18]

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении Windows Phone 8 возможностей работы с данными в мобильных службах, созданных с применением среды выполнения .NET. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов c разбиением по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с проверкой подлинности]
  <br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

<!--
* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.
-->

* [Справочник по принципам использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с .NET.
  
<!-- Anchors. -->

[Загрузка проекта приложения Windows Phone 8]: #download-app
[Создание мобильной службы]: #create-service
[Локальная загрузка мобильной службы]: #download-the-service-locally
[Обновление приложения Windows Phone для использования мобильной службы]: #update-app
[Тестирование приложения Windows Phone для службы, размещенной локально]: #test-locally-hosted
[Публикация мобильной службы в Azure]: #publish-mobile-service
[Тестирование приложения Windows Phone с помощью службы, размещенной в Azure]: #test-azure-hosted
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-data-sample-download-wp8-vs12.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/azure-ite
	ms.png
[17]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-deployment-target.png
[20]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/iis-express-tray.png
[26]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-service-and-packages-folder.png



<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js
[Серверная версия JavaScript]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8

[Пакет SDK для Windows Phone 8]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Веб-сайт с примерами кода для разработчиков]:  https://code.msdn.microsoft.com/Add-Azure-Mobile-to-a-8b906f72
[Справочник по принципам использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Класс MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[Добавление нового правила для порта в брандмауэре Windows]:  http://go.microsoft.com/fwlink/?LinkId=392240
  

<!--HONumber=42-->
