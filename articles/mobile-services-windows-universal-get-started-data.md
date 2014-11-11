<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services (universal Windows apps)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Начало работы с данными в мобильных службах (универсальное приложение Windows)

<div class="dev-center-tutorial-selector sublanding">

[Магазин Windows — C#][Магазин Windows — C#][Магазин Windows —JavaScript][Магазин Windows —JavaScript][Windows Phone][Windows Phone][iOS][iOS][Android][Android][HTML][HTML][Xamarin.iOS][Xamarin.iOS][Xamarin.Android][Xamarin.Android]

</div>

<!---<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend">.NET backend</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/"  title="JavaScript backend" class="current">JavaScript backend</a></div>-->

В этом разделе предстоит использовать мобильные службы Azure для эффективного использования данных в универсальном приложении Windows 8.1. В этом учебнике предстоит скачать проект Visual Studio для универсального приложения Windows, которое хранит данные в памяти, создать новую мобильную службу, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

> [WACOM.NOTE]Для этого учебника требуется Visual Studio 2013 с обновлением 2, который .

В этом учебнике рассматриваются следующие основные действия:

1.  [Загрузка проекта приложения для Магазина Windows][Загрузка проекта приложения для Магазина Windows]
2.  [Создание мобильной службы в Visual Studio][Создание мобильной службы в Visual Studio]
3.  [Добавление таблицы данных для хранения и обновления приложения][Добавление таблицы данных для хранения и обновления приложения]
4.  [Тестирование работы приложения с мобильными службами][Тестирование работы приложения с мобильными службами]

> [WACOM.NOTE] Чтобы выполнить работу с этим учебником, необходимо использовать учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

## <a name="download-app"></a>Скачивание проекта GetStartedWithData

Этот учебник основан на проекте [Приложение GetStartedWithMobileServices][Приложение GetStartedWithMobileServices] — проекте приложения для магазина Windows в Visual Studio 2013. Пользовательский интерфейс для этого приложения идентичен приложению, которое создается при быстром начале работы с мобильными службами, за исключением того, что добавленные элементы хранятся локально в памяти.

1.  Загрузите версию примера приложения GetStartedWithMobileServices на C# с веб-сайта [Примеры кода для разработчиков][Приложение GetStartedWithMobileServices].

    ![][0]

2.  В Visual Studio 2013 откройте загруженный проект и просмотрите файл MainPage.xaml.cs.

    Обратите внимание, что добавленные объекты **TodoItem** хранятся во внутренней памяти **ObservableCollection\<TodoItem\>**.

3.  Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4.  В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

    ![][1]

    Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запрос и обновление данных**

## <a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы в Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  В обозревателе решений откройте файл с кодом App.xaml.cs и обратите внимание на новое статическое поле, добавленное в класс \*\*App\*\*, которое выглядит как в следующем примере:

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью экземпляра [класса MobileServiceClient][класса MobileServiceClient]. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Это статическое поле доступно для всех страниц в приложении.

## <a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу и обновление приложения

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

1.  В файле MainPage.xaml.cs добавьте или раскомментируйте следующие операторы "using":

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

2.  В том же файле замените определение класса TodoItem следующим кодом:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")] 
            public bool Complete { get; set; }
        }

    Указанный **JsonPropertyAttribute** используется для определения сопоставления между именами свойств в типе клиента и именами столбцов в базовой таблице данных.

3.  Закомментируйте строку, которая определяет коллекцию существующих элементов, затем раскомментируйте или добавьте следующие строки и замените *\<вашКлиент\>* полем `MobileServiceClient`, добавленным в файл App.xaml.cs, после подключения проекта к мобильной службе:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();

    Этот код создает коллекцию привязок (элементы) и прокси-класс для таблицы базы данных (todoTable), в которых включены мобильные службы.

4.  В методе **InsertTodoItem** удалите строку кода, которая устанавливает значение свойства **TodoItem.Id**, добавьте в метод модификатор **async** и раскомментируйте следующую строку кода:

        await todoTable.InsertAsync(todoItem);

    Этот код вставляет новый элемент в таблицу.

    <div class="dev-callout">

    <b>Примечание.</b>
    Новые таблицы создаются со столбцами "Id", "__createdAt", "__updatedAt" и "__version". Если динамическая схема включена, мобильные службы автоматически создают новые столбцы на основе JSON-объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема][Динамическая схема].

    </div>

5.  В методе **RefreshTodoItems** добавьте модификатор **async**, затем раскомментируйте следующую строку кода:

        items = await todoTable.ToCollectionAsync();

    Это установит привязку к коллекции элементов в `todoTable`, которая содержит все объекты **TodoItem**, возвращенные из мобильной службы.

6.  В методе **UpdateCheckedTodoItem** добавьте модификатор **async**, затем раскомментируйте следующую строку кода:

        await todoTable.UpdateAsync(item);

    В результате в мобильную службу будет отправлено обновление элемента.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

## <a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой

1.  В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2.  Как и ранее, введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

    В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3.  На [Портале управления][Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4.  Откройте вкладку **Данные**, а затем щелкните **Обзор**.

    ![][2]

    Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

5.  В приложении отметьте один из элементов списка, а затем вернитесь на вкладку "Обзор" на портале и нажмите кнопку **Обновить**

    Обратите внимание, что значение выполнения изменилось с **false** на **true**.

6.  В файле проекта MainPage.xaml.cs замените существующий метод **RefreshTodoItems** следующим кодом, который будет отфильтровывать выполненные элементы:

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7.  В приложении отметьте другой элемент в списке, затем нажмите кнопку **Обновить**.

    Обратите внимание, что отмеченный элемент теперь исчез из списка. Каждое обновление ведет к обмену данными с мобильной службой, которая теперь будет возвращать отфильтрованные данные.

Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для магазина Windows возможностей работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

-   [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]

    Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

-   [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]

    Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]

    Дополнительные сведения о проверке подлинности пользователей приложения.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]

    Дополнительные сведения об использовании мобильных служб в .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Магазин Windows — C#]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/ "Магазин Windows — C#"
  [Магазин Windows —JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "Магазин Windows — JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data/ "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-android-get-started-data/ "Android"
  [HTML]: /ru-ru/documentation/articles/mobile-services-html-get-started-data/ "HTML"
  [Xamarin.iOS]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/ "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/ "Xamarin.Android"
  [Загрузка проекта приложения для Магазина Windows]: #download-app
  [Создание мобильной службы в Visual Studio]: #create-service
  [Добавление таблицы данных для хранения и обновления приложения]: #add-table
  [Тестирование работы приложения с мобильными службами]: #test-app
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28
  [Приложение GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [класса MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Динамическая схема]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193175.aspx
  [Портале управления]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
  [Приступая к работе с push-уведомлениями]: ../mobile-services-windows-store-dotnet-get-started-push/
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
