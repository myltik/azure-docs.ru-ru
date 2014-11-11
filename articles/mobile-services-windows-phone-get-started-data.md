<properties linkid="develop-mobile-tutorials-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (WP8) - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data from your Azure Mobile Services Windows Phone 8 app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Приступая к работе с данными в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении для Windows Phone 8. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">12:54:00</span></div>

</div>

<div class="dev-callout"><b>Примечание.</b>
<p>Этот учебник поможет вам лучше понять, как с помощью мобильных служб можно использовать Azure для хранения и извлечения данных из приложения для Windows Phone 8. В этом разделе рассматриваются многие действия, которые выполняются в кратком руководстве по использованию мобильных служб. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="/ru-ru/develop/mobile/tutorials/get-started-wp8">Приступая к работе с мобильными службами</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1.  [Загрузка проекта приложения Windows Phone 8][Загрузка проекта приложения Windows Phone 8]
2.  [Создание мобильной службы][Создание мобильной службы]
3.  [Добавление таблицы для хранения данных][Добавление таблицы для хранения данных]
4.  [Обновление приложения для использования мобильных служб][Обновление приложения для использования мобильных служб]
5.  [Тестирование работы приложения с мобильными службами][Тестирование работы приложения с мобильными службами]

Для работы с этим учебником требуется Visual Studio 2012 Express для Windows Phone 8 и пакет [SDK для Windows Phone 8][SDK для Windows Phone 8], запущенный в среде Windows 8. Для выполнения действий из этого учебника и создания приложения для Windows Phone 8.1 требуется Visual Studio 2013 с обновлением 2 или более поздней версии.

> [WACOM.NOTE] Чтобы выполнить работу с этим учебником, необходимо использовать учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

## <a name="download-app"></a>Скачивание проекта GetStartedWithData

Этот учебник основан на проекте приложения для Windows Phone Silverlight 8, [GetStartedWithData app][GetStartedWithData app]. Пользовательский интерфейс для этого приложения совпадает с интерфейсом приложения, созданного в кратком руководстве по использованию мобильных служб. Отличие заключается в том, что добавленные элементы хранятся локально в памяти.

1.  Скачайте пример проекта приложения GetStartedWithData с веб-сайта [примеров кода для разработчиков][GetStartedWithData app].

    > [WACOM.NOTE]Чтобы создать приложение для Windows Phone Silverlight 8.1, измените целевую ОС в скачанном проекте приложения для Windows Phone Silverlight 8 на Windows Phone 8.1. Чтобы создать приложение для Магазина Windows Phone, при скачивании примера проекта приложения GetStartedWithData выберите версию [приложения для Магазина Windows Phone][приложения для Магазина Windows Phone].

2.  В Visual Studio откройте скачанный проект и просмотрите файл MainPage.xaml.cs.

    Обратите внимание, что добавленные объекты **TodoItem** хранятся во внутренней памяти в **ObservableCollection\<TodoItem\>**.

3.  Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4.  В приложении введите какой-либо текст в текстовом поле, а затем щелкните кнопку **Сохранить**.

    ![][0]

    Обратите внимание, что сохраненный текст отображается в списке ниже.

## <a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы на портале управления

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для использования мобильной службы для доступа к данным

После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции.

1.  В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управлять пакетами NuGet**.

2.  На левой панели выберите категорию **Интернет**, выполните поиск по запросу `WindowsAzure.MobileServices`, щелкните **Установить** для пакета **Мобильные службы Windows Azure**, а затем примите условия лицензионного соглашения.

    ![][1]

    При этом клиентская библиотека мобильных служб будет добавлена в проект.

3.  На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

4.  Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

    ![][2]

    Эти значения потребуются при обращении к мобильной службе из кода приложения.

5.  В Visual Studio откройте файл App.xaml.cs и добавьте или раскомментируйте следующую инструкцию `using`:

        using Microsoft.WindowsAzure.MobileServices;

6.  В том же файле раскомментируйте следующий код, который определяет переменную **MobileService**, и укажите URL-адрес и ключ приложения мобильной службы в конструкторе **MobileServiceClient** в указанном порядке.

        //public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

    Код создает экземпляр **MobileServiceClient**, используемый для доступа к мобильной службе.

7.  В файле MainPage.xaml.cs добавьте или раскомментируйте следующие инструкции `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

8.  В том же файле замените определение класса **TodoItem** следующим кодом:

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

9.  Закомментируйте строку, которая определяет существующую коллекцию **items**, а затем раскомментируйте следующие строки:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.MobileService.GetTable<TodoItem>();

    Этот код создает коллекцию привязок с поддержкой мобильных служб (**items**) и прокси-класс для таблицы базы данных SQL **TodoItem** (**todoTable**).

10. В методе **InsertTodoItem** удалите строку кода, которая устанавливает значение свойства **TodoItem**.**Id**, добавьте в метод модификатор **async** и раскомментируйте следующую строку кода:

        await todoTable.InsertAsync(todoItem);

    Этот код вставляет новый элемент в таблицу.

11. В методе **RefreshTodoItems** добавьте модификатор **async**, затем раскомментируйте следующую строку кода:

        items = await todoTable.ToCollectionAsync();

    Это установит привязку к коллекции элементов в todoTable, которая содержит все объекты TodoItem, возвращенные из мобильной службы.

12. В методе **UpdateCheckedTodoItem** добавьте модификатор **async**, затем раскомментируйте следующую строку кода:

         await todoTable.UpdateAsync(item);

    В результате в мобильную службу будет отправлено обновление элемента.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

## <a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой

1.  В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2.  Как и ранее, введите текст в текстовом поле и нажмите кнопку **Сохранить**.

    В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3.  На [Портале управления][Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4.  Откройте вкладку **Данные**, а затем щелкните **Обзор**.

    ![][3]

    Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

Это заключительный шаг учебника **Приступая к работе с данными** для Windows Phone 8.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для Windows Phone 8 возможностей работы с данными в мобильных службах. Далее рассмотрите выполнение следующего учебника, основанном на приложении GetStartedWithData, создание которого описано в этом учебнике:

-   [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]
    <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

-   [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]
    <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После завершения ряда данных можно попробовать выполнить один из следующих учебников по Windows Phone 8.

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    <br/>Дополнительные сведения о проверке подлинности пользователей приложения.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    <br/>Сведения об отправке в приложение простейших push-уведомлений с помощью мобильных служб.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [Загрузка проекта приложения Windows Phone 8]: #download-app
  [Создание мобильной службы]: #create-service
  [Добавление таблицы для хранения данных]: #add-table
  [Обновление приложения для использования мобильных служб]: #update-app
  [Тестирование работы приложения с мобильными службами]: #test-app
  [SDK для Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F
  [GetStartedWithData app]: http://go.microsoft.com/fwlink/p/?LinkId=271146
  [приложения для Магазина Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  [0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [1]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
  [2]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
  [Портале управления]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png
  [Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8
  [Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8
