<properties urlDisplayName="Get Started with Data" pageTitle="Начало работы с данными (Windows Phone) | Центр мобильных разработок" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Phone app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Добавление мобильных служб к существующему приложению

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

В этом разделе показано, как добавить мобильные службы Azure в качестве серверного источника данных для приложения Windows Phone 8.0 или Windows Phone 8.1 Silverlight. В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для приложения, хранящего данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

Мобильная служба, создаваемая в этом учебнике, поддерживает среду выполнения .NET в мобильной службе. Это позволит использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе. Для создания мобильной службы, которая позволяет писать серверную бизнес-логику на языке JavaScript, ознакомьтесь с разделом [Серверная версия базы данных JavaScript] этого раздела.


В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения Windows Phone 8]
2. [Создание мобильной службы]
3. [Локальная загрузка мобильной службы]
4. [Обновление приложения Windows Phone для использования мобильной службы]
5. [Тестирование приложения Windows Phone для службы, размещенной локально]
6. [Публикация мобильной службы в Azure]
7. [Тестирование приложения Windows Phone для службы, размещенной в Azure]

Для работы с данным учебником требуется следующее:

+ Visual Studio 2013 и [Windows Phone 8 SDK] на базе ОС Windows 8. Чтобы выполнить задания этого учебника по созданию приложения Windows Phone 8.1 Silverlight, вам потребуется Visual Studio 2013 Update 2 или более поздней версии.
+ Учетная запись Microsoft Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div> 

##<a name="download-app"></a>Загрузка пакета GetStartedWithData

Этот учебник основан на [приложении GetStartedWithMobileServices][ с сайта с примерами кода от разработчиков] - это проект приложения Windows Phone Silverlight 8.  

1. Загрузите версию примера GetStartedWithMobileServices на C# с [веб-сайта с примерами кода для разработчиков]. 

   	![][1]

	>[WACOM.NOTE]Чтобы создать приложение Windows Phone Silverlght 8.1, достаточно сменить целевую ОС в загруженном проекте приложения Windows Phone Silverlight 8 на Windows Phone 8.1. Чтобы создать приложение для Магазина Windows Phone, при скачивании примера проекта приложения GetStartedWithData выберите версию [приложения для Магазина Windows Phone](http://go.microsoft.com/fwlink/p/?LinkId=397372).

2. Запустите Visual Studio 2013 с правами администратора, для чего щелкните правой кнопкой мыши Visual Studio и выберите **Запуск от имени администратора**.

3. В Visual Studio откройте скачанный проект и просмотрите файл MainPage.xaml.cs.

   	Обратите внимание, что добавленные объекты **TodoItem** хранятся в **ObservableCollection<TodoItem>** в памяти.

4. В Visual Studio выберите целевой объект развертывания для приложения. Можно выполнить развертывание на устройство Windows Phone или в один из эмуляторов, входящих в пакет Windows Phone SDK. В этом учебнике мы покажем, как выполнять развертывание на эмуляторе.

    ![][19]

5. Нажмите клавишу **F5**. При этом выполняется сборка, развертывание и запуск приложения для отладки.

6. В приложении введите любой текст в текстовом поле, а затем нажмите кнопку **Сохранить**, чтобы сохранить несколько элементов в памяти.

   	![][0]  

   	Заметьте, что для каждого элемента `TodoItem` под кнопкой обновления вместе с текстом отображается флажок, который позволяет помечать выполненные дела.

<h2><a name="create-service"></a>Создание мобильной службы</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]


<h2><a name="download-the-service-locally"></a>Загрузка проекта мобильной службы и его добавление в решение</h2>

1. На [портале управления Azure] щелкните новую мобильную службу или вкладку с ее облачным значком, чтобы перейти на страницу обзора.

    ![][2]

2. Щелкните платформу **Windows Phone 8**. В разделе **Начало работы** разверните элемент **Подключение существующего приложения Windows Phone 8**, а затем щелкните **Загрузить**, чтобы загрузить персональный начальный проект для мобильной службы. 

    ![][3]

3. В том же разделе щелкните ссылку, показанную на представленном ниже снимке экрана, чтобы скачать файл профиля публикации для только что скачанной мобильной службы. 

    > [WACOM.NOTE]  Сохраните файл в безопасном месте, поскольку он содержит важные сведения, относящиеся к вашей учетной записи Azure. Вы удалите этот файл после публикации мобильной службы далее в этом учебнике. 

    ![][5]


4. Распакуйте загруженный персональный начальный проект службы. Скопируйте папки, находившиеся в ZIP-файле, в один каталог **C#**, в котором находится файл решения для начала работы с данными (SLN-файл). Так диспетчеру пакетов NuGet будет проще обеспечивать синхронизацию всех пакетов.

    ![][26]


5. В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение для начала работы с данными приложения Магазина Windows. Щелкните **Добавить**, затем щелкните **Существующий проект**.

    ![][4]

6. В диалоговом окне "Добавление существующего проекта" перейдите к папке проекта мобильной службы, которую вы перенесли в каталог **C#**. В подкаталоге службы выберите файл проекта C# (с расширением .csproj). Щелкните **Открыть**, чтобы добавить проект в решение.

    ![][6]

7. В обозревателе решений Visual Studio щелкните правой кнопкой мыши только что добавленный проект службы и выберите **Сборка**, чтобы убедиться, что сборка выполняется без ошибок. Во время сборки диспетчеру пакетов NuGet может потребоваться восстановить некоторые пакеты NuGet, на которые имеются ссылки в проекте.

    ![][20]

8. Еще раз щелкните правой кнопкой мыши проект службы. На этот раз выберите команду **Запустить новый экземпляр** в контекстном меню **Отладка**.

    ![][21]

    В Visual Studio откроется стандартная веб-страница службы. Щелкните **Проверить**, чтобы протестировать методы мобильной службы на стандартной веб-странице.

    ![][22]

    Visual Studio по умолчанию размещает мобильную службу как локальную в IIS Express. Это можно увидеть, щелкнув правой кнопкой мыши значок IIS Express на панели задач.

    ![][23]


##<a name="update-app"></a>Обновление приложения Windows Phone для использования мобильной службы

В этом разделе вы обновите приложение Windows Phone, чтобы использовать мобильную службу в качестве серверной службы для приложения.


1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект приложения Windows Phone и выберите **Управление пакетами NuGet**.

    ![][7]

2. В диалоговом окне "Управление пакетами NuGet" выполните поиск **WindowsAzure.MobileServices** в коллекции сетевых пакетов и щелкните, чтобы установить Nuget-пакет мобильных служб Azure. Затем закройте диалоговое окно.

    ![][8]

3. Вернитесь на портал управления Azure и найдите действие **Подключение приложения и хранение данных в службе**. Скопируйте фрагмент кода, который создает подключение `MobileServiceClient`.

    ![][9]

4. В Visual Studio откройте файл App.xaml.cs. Вставьте фрагмент кода в начало определения класса `App`. Вверху файла добавьте также следующую инструкцию `using` и сохраните файл.

		using Microsoft.WindowsAzure.MobileServices;

    ![][10]


5. В Visual Studio откройте файл MainPage.xaml.cs и в верхней части файла добавьте следующую инструкцию using: 

		using Microsoft.WindowsAzure.MobileServices;

6. В файле MainPage.xaml.cs, открытом в Visual Studio, замените определение класса `MainPage` приведенным ниже определением, после чего сохраните файл.

    Этот код использует пакет Mobile Services SDK, что позволяет приложению хранить данные в таблице, предоставленной службой, а не размещать их локально в памяти. Три основных метода: `InsertTodoItem`, `RefreshTodoItems` и `UpdateCheckedTodoItem`. Эти три метода позволяют асинхронно вставлять, запрашивать и обновлять коллекцию данных в таблице Azure. 

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
                items.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable 
                    .ToCollectionAsync(); 
                ListItems.ItemsSource = items;
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




##<a name="test-locally-hosted"></a>Тестирование приложения Windows Phone со службой, размещенной локально</h2>

В этом разделе вы научитесь использовать Visual Studio для локального тестирования приложения и мобильной службы на компьютере, где ведете разработку. Чтобы протестировать мобильную службу, размещенную локально в IIS Express, с устройства Windows Phone или одного из эмуляторов Windows Phone, необходимо настроить IIS Express и рабочую станцию таким образом, чтобы разрешить подключения к IP-адресу и порту рабочей станции. Устройства и эмуляторы Windows Phone подключаются как нелокальные сетевые клиенты.

#### Настройка IIS Express для использования удаленных подключений

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]

#### Тестирование приложения с мобильной службой в IIS Express

6. В Visual Studio откройте файл App.xaml.cs и закомментируйте определение `MobileService`, которое вы недавно вставили в файл. Добавьте новое определение для подключения на основе IP-адреса и порта, настроенных на рабочей станции. Затем сохраните файл. Код должен выглядеть следующим образом:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "http://192.168.111.11:54321");

        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "https://todolist.azure-mobile.net/",
        //    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        //);        


7. В Visual Studio нажмите клавишу F7 или щелкните **Построить решение** в меню "Сборка", чтобы создать приложение Windows Phone и мобильную службу. В окне вывода Visual Studio проверьте, есть ли ошибки в проектах

    ![][11]

8. В Visual Studio нажмите клавишу F5 или щелкните **Начать отладку** в меню "Отладка", чтобы запустить приложение и разместить мобильную службу локально в IIS Express. 

    >[WACOM.NOTE]  Запускайте Visual Studio с параметром **Запуск от имени администратора**. Иначе IIS Express может не подгрузить изменения в файле applicationhost.config.

    ![][12]


9. Введите новый текст элемента todoitem. Нажмите кнопку **Сохранить**. При этом новый элемент todoItem вставляется в базу данных, которая создается мобильной службой, размещенной локально в IIS Express. Установите флажок для одного из элементов, чтобы пометить его как завершенный.

    ![][15]

10. В Visual Studio остановите отладку приложения. Чтобы посмотреть изменения в базе данных, созданной для серверной службы, откройте обозреватель сервера и разверните элемент "Подключения к данным". Щелкните правой кнопкой мыши таблицу TodoItems в разделе **MS_TableConnectionString** и выберите пункт **Показать таблицу данных**.

    ![][14]

11. После завершения тестирования с размещенной локально мобильной службой удалите созданное вами правило брандмауэра Windows, открывающее порт на вашей рабочей станции.


##<a name="publish-mobile-service"></a>Публикация мобильной службы в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Проверка мобильной службы, опубликованной в Azure

1. В Visual Studio откройте файл App.xaml.cs.  Закомментируйте код, который создает объект `MobileServiceClient`, подключающийся к локально размещенной мобильной службе. Раскомментируйте код, который создает объект `MobileServiceClient`, подключающийся к вашей службе в Azure. Сохраните изменения в файл.

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

2. В Visual Studio нажмите клавишу F5 или щелкните **Начать отладку** в меню "Отладка". В результате перед запуском выполняется повторная сборка приложения с учетом предыдущего изменения, только потом оно запустится для подключения к мобильной службе, размещенной в Azure. 

    ![][12]

3. Введите новые элементы todoitem и для каждого из них щелкните **Сохранить**. Установите флажок для завершения некоторых новых элементов. Каждый новый элемент todoItem будет храниться и обновляться в базе данных SQL, ранее настроенной вами для мобильной службы на портале управления Azure. 

    ![][16]

    Можно перезапустить приложение, чтобы проверить, сохранены ли изменения в базе данных в Azure. Можно также проверить базу данных с помощью портала управления Azure или обозревателя объектов SQL Server в Visual Studio. В следующих двух действиях для просмотра изменений в базе данных будет использоваться портал управления Azure.


4. На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![][17]

5. На портале управления выполните запрос для просмотра изменений, внесенных приложением. Этот запрос будет аналогичен следующему запросу, но используйте имя своей базы данных вместо `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][18]

На этом прохождение учебника **Начало работы с данными** завершается.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы использования приложения Windows Phone 8 для работы с данными в мобильных службах, созданных с применением среды выполнения .NET. Далее рассмотрите выполнение одного из следующих учебников, который основан на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Использование скриптов сервера в мобильных службах для проверки и изменения данных, отправленных из приложения.

* [Уточнение запросов посредством разбиения по страницам]
  <br/>Использование разбивки на страницы в запросах для управления количеством данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения.

<!--
* [Get started with push notifications] 
  <br/>Отправка очень простого push-уведомления в приложение.
-->

* [Справочник принципов использования мобильных служб .NET]
  <br/>Использование мобильных служб с .NET.
  
<!-- Anchors. -->

[Загрузка проекта приложения Windows Phone 8]: #download-app
[Создание мобильной службы]: #create-service
[Локальная загрузка мобильной службы]: #download-the-service-locally
[Обновление приложения Windows Phone для использования мобильной службы]: #update-app
[Тестирование приложения Windows Phone для службы, размещенной локально]: #test-locally-hosted
[Публикация мобильной службы в Azure]: #publish-mobile-service
[Тестирование приложения Windows Phone для службы, размещенной в Azure]: #test-azure-hosted
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
[16]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/azure-items.png
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
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js
[Серверная версия JavaScript]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8

[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?linkid=271146
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Класс MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[Добавление нового правила для порта в брандмауэре Windows]:  http://go.microsoft.com/fwlink/?LinkId=392240
  
