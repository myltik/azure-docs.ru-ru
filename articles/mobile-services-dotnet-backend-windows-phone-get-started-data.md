<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Phone app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc"></tags>

# Приступая к работе с данными в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy][mobile-services-selector-get-started-data-legacy]]

В этом разделе показано, как использовать мобильные службы Azure в качестве серверного источника данных для приложения для Windows Phone (Silverlight). В этом учебнике вам предстоит скачать проект Visual Studio для приложения, хранящего данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением и просмотреть изменения, внесенные в данные во время работы приложения.

Создаваемая в этом учебнике мобильная служба будет поддерживать среду выполнения .NET в компоненте мобильных служб. Это позволит использовать языки .NET и Visual Studio для серверной бизнес-логики в мобильной службе. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript][Серверная версия JavaScript] этого раздела.

В этом учебнике рассматриваются следующие основные действия:

1.  [Загрузка проекта приложения Windows Phone 8][Загрузка проекта приложения Windows Phone 8]
2.  [Создание мобильной службы][Создание мобильной службы]
3.  [Локальная загрузка мобильной службы][Локальная загрузка мобильной службы]
4.  [Обновление приложения Windows Phone для использования мобильной службы][Обновление приложения Windows Phone для использования мобильной службы]
5.  [Тестирование приложения Windows Phone для службы, размещенной локально][Тестирование приложения Windows Phone для службы, размещенной локально]
6.  [Публикация мобильной службы в Azure][Публикация мобильной службы в Azure]
7.  [Тестирование приложения Windows Phone для службы, размещенной в Azure][Тестирование приложения Windows Phone для службы, размещенной в Azure]

Для работы с данным учебником требуется следующее:

-   Visual Studio 2013 и [пакет SDK для Windows Phone 8][пакет SDK для Windows Phone 8], работающий в среде Windows 8. Чтобы завершить этот учебник и создать приложение для Windows Phone 8.1, следует использовать Visual Studio 2013 с обновлением 2 или более позднюю версию.
-   Учетная запись Microsoft Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].
    </p>
    </div>

## <a name="download-app"></a>Скачивание проекта GetStartedWithData

Этот учебник основан на проекте приложения для Windows Phone Silverlight 8 [GetStartedWithMobileServices][GetStartedWithMobileServices]. Пользовательский интерфейс для этого приложения похож на интерфейс приложения, созданного в кратком руководстве по использованию мобильных служб. Отличие заключается в том, что добавленные элементы хранятся локально в памяти.

1.  Загрузите версию примера приложения GetStartedWithMobileServices на C# с веб-сайта [Примеры кода для разработчиков][GetStartedWithMobileServices].

    ![][]

    > [WACOM.NOTE]Чтобы создать приложение для Windows Phone Silverlight 8.1, измените целевую ОС в скачанном проекте приложения для Windows Phone Silverlight 8 на Windows Phone 8.1. Чтобы создать приложение для Магазина Windows Phone, при скачивании примера проекта приложения GetStartedWithData выберите версию [приложения для Магазина Windows Phone][приложения для Магазина Windows Phone].

2.  Запустите Visual Studio с привилегиями администратора. Для этого щелкните правой кнопкой мыши Visual Studio и выберите **Запуск от имени администратора**.

3.  В Visual Studio откройте скачанный проект и просмотрите файл MainPage.xaml.cs.

    Обратите внимание, что добавленные объекты **TodoItem** хранятся во внутренней памяти в **ObservableCollection\<TodoItem\>**.

4.  В Visual Studio выберите целевой объект развертывания для приложения. Можно выполнить развертывание на устройство Windows Phone или в один из эмуляторов, входящих в пакет Windows Phone SDK. В этом учебнике будет показано развертывание в эмуляторе.

    ![][1]

5.  Нажмите клавишу **F5**. При этом выполняется сборка, развертывание и запуск приложения для отладки.

6.  В приложении введите любой текст в текстовом поле, а затем щелкните **Сохранить**, чтобы сохранить несколько элементов во внутренней памяти приложения.

    ![][2]

    Заметьте, что для каждого элемента `TodoItem` текст отображается под кнопкой обновления вместе с флажком, который позволяет помечать завершенные элементы.

## <a name="create-service"></a><span class="short-header">Создание новой мобильной службы</span>Создание новой мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## <a name="download-the-service-locally"></a><span class="short-header">Локальное скачивание службы</span>Скачивание проекта мобильной службы и добавление его в решение

1.  На [портале управления Azure][портале управления Azure] щелкните новую мобильную службу или вкладку с ее облачным значком, чтобы перейти на страницу обзора.

    ![][3]

2.  Щелкните платформу **Windows Phone 8**. В разделе **Начало работы** разверните элемент **Подключение существующего приложения Windows Phone 8**, а затем щелкните **Загрузить**, чтобы загрузить персональный начальный проект для мобильной службы.

    ![][4]

3.  В том же разделе щелкните ссылку, показанную на представленном ниже снимке экрана, чтобы скачать файл профиля публикации для только что скачанной мобильной службы.

    > [WACOM.NOTE] Сохраните файл в безопасном месте, поскольку он содержит конфиденциальные сведения, относящиеся к вашей учетной записи Azure. Вы удалите этот файл после публикации мобильной службы позднее в этом учебнике.

    ![][5]

4.  Распакуйте скачанный персонализированный начальный проект службы. Скопируйте папки, находившиеся в ZIP-файле, в один каталог **C#**, в котором находится файл решения для начала работы с данными (SLN-файл). Так диспетчеру пакетов NuGet будет проще обеспечивать синхронизацию всех пакетов.

    ![][6]

5.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши решение для начала работы с приложением для обработки данных для Магазина Windows. Щелкните **Добавить**, затем щелкните **Существующий проект**.

    ![][7]

6.  В диалоговом окне «Добавление существующего проекта» перейдите к папке проекта мобильной службы, которую вы перенесли в каталог **С\#**. В подкаталоге службы выберите файл проекта C# (с расширением .csproj). Щелкните **Открыть**, чтобы добавить проект в решение.

    ![][8]

7.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши только что добавленный проект службы и выберите **Сборка**, чтобы убедиться, что сборка выполняется без ошибок. Во время сборки диспетчеру пакетов NuGet может потребоваться восстановить некоторые пакеты NuGet, на которые имеются ссылки в проекте.

    ![][9]

8.  Еще раз щелкните правой кнопкой мыши проект службы. На этот раз выберите команду **Запустить новый экземпляр** в контекстном меню **Отладка**.

    ![][10]

    В Visual Studio открывается веб-страница по умолчанию для вашей службы. Щелкните **Попробовать сейчас**, чтобы с веб-страницы по умолчанию протестировать методы в мобильной службе.

    ![][11]

    Visual Studio по умолчанию размещает мобильную службу локально в IIS Express. Чтобы увидеть это, щелкните правой кнопкой мыши значок для IIS Express на панели задач.

    ![][12]

## <a name="update-app"></a>Изменение приложения для Windows Phone для использования мобильной службы

В этом разделе вы обновите приложение Windows Phone, чтобы использовать мобильную службу в качестве серверной службы для приложения.

1.  В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект приложения для Магазина Windows и выберите **Управление пакетами NuGet**.

    ![][13]

2.  В диалоговом окне «Управление пакетами NuGet» выполните поиск **WindowsAzure.MobileServices** в коллекции пакетов в Интернете и щелкните пакет Nuget для мобильных служб Azure, чтобы установить его. Затем закройте диалоговое окно.

    ![][14]

3.  Вернитесь на портал управления Azure и найдите шаг **Подключение приложения и хранение данных в службе**. Скопируйте фрагмент кода, который создает подключение `MobileServiceClient`.

    ![][15]

4.  В Visual Studio откройте файл App.xaml.cs. Вставьте фрагмент кода в начало определения класса `App`. Также добавьте следующую инструкцию `using` в верхнюю часть этого файла, затем сохраните файл.

        using Microsoft.WindowsAzure.MobileServices;

    ![][16]

5.  В Visual Studio откройте файл MainPage.xaml.cs и в верхней части файла добавьте следующую инструкцию using:

        using Microsoft.WindowsAzure.MobileServices;

6.  В файле MainPage.xaml.cs, открытом в Visual Studio, замените определение класса `MainPage` приведенным ниже определением, после чего сохраните файл.

    Этот код использует пакет Mobile Services SDK, чтобы приложение могло хранить данные в таблице, предоставленной службой, а не размещать их локально в памяти. Три основных метода: `InsertTodoItem`, `RefreshTodoItems` и `UpdateCheckedTodoItem`. Эти три метода позволяют асинхронно вставлять, запрашивать и обновлять коллекцию данных в таблице в Azure.

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

## <a name="test-locally-hosted"></a>Тестирование приложения для Windows Phone для службы, размещенной локально

</h2>
В этом разделе вы будете использовать Visual Studio для локального тестирования приложения и мобильной службы на рабочей станции разработки. Чтобы протестировать мобильную службу, размещенную локально в IIS Express, с устройства Windows Phone или одного из эмуляторов Windows Phone, необходимо настроить IIS Express и рабочую станцию таким образом, чтобы разрешить подключения к IP-адресу и порту рабочей станции. Устройства и эмуляторы Windows Phone подключаются как нелокальные сетевые клиенты.

#### Настройка IIS Express для использования удаленных подключений

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express][mobile-services-how-to-configure-iis-express]]

#### Тестирование приложения с мобильной службой в IIS Express

1.  В Visual Studio откройте файл App.xaml.cs и закомментируйте определение `MobileService`, которое вы недавно вставили в файл. Добавьте новое определение для подключения на основе IP-адреса и порта, настроенных на рабочей станции. Затем сохраните файл. Код должен выглядеть следующим образом:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "http://192.168.111.11:54321");

        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "https://todolist.azure-mobile.net/",
        //    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        //);        

2.  В Visual Studio нажмите клавишу F7 или щелкните **Собрать решение** в меню «Сборка», чтобы создать приложение для Windows Phone и мобильную службу. В окне вывода Visual Studio убедитесь, что оба проекта создаются без ошибок.

    ![][17]

3.  В Visual Studio нажмите клавишу F5 или щелкните **Начать отладку** в меню «Отладка», чтобы запустить приложение и разместить мобильную службу локально в IIS Express.

    > [WACOM.NOTE] Запускайте Visual Studio с параметром **Запуск от имени администратора**. Иначе в IIS Express могут не загрузиться изменения applicationhost.config.

    ![][18]

4.  Введите новый текст элемента todoitem. Нажмите кнопку **Сохранить**. При этом новый элемент todoItem вставляется в базу данных, которая создается мобильной службой, размещенной локально в IIS Express. Установите флажок для одного из элементов, чтобы пометить его как завершенный.

    ![][19]

5.  В Visual Studio остановите отладку приложения. Чтобы посмотреть изменения в базе данных, созданной для серверной службы, откройте обозреватель сервера и разверните элемент "Подключения к данным". Щелкните правой клавишей таблицу TodoItems в **MS\_TableConnectionString** и щелкните **Показать данные таблицы**.

    ![][20]

6.  После завершения тестирования с размещенной локально мобильной службой удалите созданное вами правило брандмауэра Windows, открывающее порт на вашей рабочей станции.

## <a name="publish-mobile-service"></a>Публикация мобильной службы в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## <a name="test-azure-hosted"></a>Тестирование мобильной службы, опубликованной в Azure

1.  В Visual Studio откройте файл App.xaml.cs. Закомментируйте код, который создает объект `MobileServiceClient`, подключающийся к локально размещенной мобильной службе. Раскомментируйте код, который создает объект `MobileServiceClient`, подключающийся к вашей службе в Azure. Сохраните изменения в файле.

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

2.  В Visual Studio нажмите клавишу F5 или щелкните **Начать отладку** в меню «Отладка». В результате перед запуском приложения для подключения к мобильной службе, размещенной удаленно в Azure, выполняется повторная сборка приложения с учетом предыдущего изменения.

    ![][18]

3.  Введите новые элементы todoitem и для каждого из них щелкните **Сохранить**. Установите флажок для завершения некоторых новых элементов. Каждый новый элемент todoItem будет храниться и обновляться в базе данных SQL, ранее настроенной вами для мобильной службы на портале управления Azure.

    ![][21]

    Можно перезапустить приложение, чтобы убедиться, что изменения были сохранены в базе данных в Azure. Можно также проверить базу данных с помощью портала управления Azure или обозревателя объектов SQL Server в Visual Studio. В следующих двух действиях будет использоваться портал управления Azure для просмотра изменений в базе данных.

4.  На портале управления щелкните управление для базы данных, связанной с вашей мобильной службой.

    ![][22]

5.  На портале управления выполните запрос для просмотра изменений, внесенных приложением. Ваш запрос будет аналогичен показанному ниже, но вместо `todolist` укажите имя вашей базы данных.

        SELECT * FROM [todolist].[todoitems]

    ![][23]

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы использования приложения Windows Phone 8 для работы с данными в мобильных службах, созданных с использованием среды выполнения .NET. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

-   [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]

    Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

-   [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]

    Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    Дополнительные сведения о проверке подлинности пользователей приложения.

<!-- * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app. -->

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]
    Дополнительные сведения об использовании мобильных служб в .NET.



  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [Серверная версия JavaScript]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
  [Загрузка проекта приложения Windows Phone 8]: #download-app
  [Создание мобильной службы]: #create-service
  [Локальная загрузка мобильной службы]: #download-the-service-locally
  [Обновление приложения Windows Phone для использования мобильной службы]: #update-app
  [Тестирование приложения Windows Phone для службы, размещенной локально]: #test-locally-hosted
  [Публикация мобильной службы в Azure]: #publish-mobile-service
  [Тестирование приложения Windows Phone для службы, размещенной в Azure]: #test-azure-hosted
  [пакет SDK для Windows Phone 8]: http://go.microsoft.com/fwlink/p/?linkid=268374
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?linkid=271146
  []: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-data-sample-download-wp8-vs12.png
  [приложения для Магазина Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-deployment-target.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/app-view.png
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [портале управления Azure]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-service-overview-page.png
  [4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-service-project.png
  [5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-publishing-profile.png
  [6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-service-and-packages-folder.png
  [7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-service-project-to-solution.png
  [8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-existing-project-dialog.png
  [9]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-service-project.png
  [10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-start-debug-service-project.png
  [11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/service-welcome-page.png
  [12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/iis-express-tray.png
  [13]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-manage-nuget-packages.png
  [14]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-nuget-packages.png
  [15]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-mobileserviceclient-snippet.png
  [16]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-pasted-mobileserviceclient.png
  [mobile-services-how-to-configure-iis-express]: ../includes/mobile-services-how-to-configure-iis-express.md
  [17]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-solution.png
  [18]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-run-solution.png
  [19]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/local-item-checked.png
  [20]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-show-local-table-data.png
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [21]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/azure-items.png
  [22]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-sql-azure-database.png
  [23]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/sql-azure-query.png
  [Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
