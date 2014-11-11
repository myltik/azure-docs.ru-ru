<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Приступая к работе с данными в мобильных службах с помощью Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Магазин Windows &mdash; JavaScript" class="current">Магазин Windows &mdash;JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-html" title="HTML" class="current">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

В этом разделе показано, как использовать мобильные службы Azure для эффективного использования данных в приложении Магазина Windows. В этом учебнике предстоит загрузить проект для приложения, которое хранит данные в памяти, создать новые услуги мобильной связи, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

<div class="dev-callout"><b>Примечание.</b>
<p>В этом учебнике функции мобильных служб добавляются в приложение для Магазина Windows, созданное в Visual Studio 2012. Visual Studio 2013 предоставляет новые возможности, которые упрощают подключение приложения для Магазина Windows к мобильным службам. Дополнительную информацию см. в разделе <a href="/ru-ru/develop/mobile/tutorials/get-started-with-data-js/">Приступая к работе с данными в мобильных службах</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные действия:

1.  [Загрузка проекта приложения для Магазина Windows][Загрузка проекта приложения для Магазина Windows]
2.  [Создание мобильной службы][Создание мобильной службы]
3.  [Добавление таблицы для хранения данных][Добавление таблицы для хранения данных]
4.  [Обновление приложения для использования мобильных служб][Обновление приложения для использования мобильных служб]
5.  [Тестирование работы приложения с мобильными службами][Тестирование работы приложения с мобильными службами]

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Загрузка проекта</span>Загрузка проекта GetStartedWithData

Этот учебник основан на приложении Магазина Windows [GetStartedWithData][GetStartedWithData]. Пользовательский интерфейс для этого приложения совпадает с интерфейсом приложения, созданного в кратком руководстве по использованию мобильных служб. Отличие заключается в том, что добавленные элементы хранятся локально в памяти.

1.  Скачайте версию примера приложения GetStartedWithData, созданную на JavaScript , с веб-сайта [примеров кода для разработчиков][GetStartedWithData].

    ![][0]

2.  В Visual Studio 2012 Express для Windows 8 откройте скачанный проект, разверните папку **js** и просмотрите файл default.js.

    Обратите внимание, что добавленные объекты **TodoItem** хранятся в объекте **List** в локальной памяти.

3.  Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4.  В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

    ![][1]

    Обратите внимание, что сохраненный текст отображается во втором столбце в разделе **Запрос и обновление данных**

## <a name="create-service"></a><span class="short-header">Создание мобильной службы</span>Создание новой мобильной службы на портале управления

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Добавление новой таблицы</span>Добавление новой таблицы в мобильную службу

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для использования мобильной службы для доступа к данным

После настройки мобильной службы вы можете обновить приложение, чтобы хранить элементы в мобильных службах, а не в локальной коллекции.

1.  В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управлять пакетами NuGet**.

2.  На левой панели выберите категорию **В сети**, выполните поиск `WindowsAzure.MobileServices.WinJS`, щелкните пункт **Установить** для пакета **Мобильные службы Azure для WinJS**, а затем примите условия лицензионного соглашения.

    ![][2]

    При этом клиентская библиотека мобильных служб будет добавлена в проект.

3.  В файле проекта default.html добавьте следующую ссылку на сценарий в заголовок страницы:

        <script type="text/javascript" src="/js/MobileServices.js"></script>

4.  На портале управления щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

5.  Щелкните вкладку **Панель мониторинга** и запишите **URL-адрес** сайта, затем щелкните **Управление ключами** и запишите **Ключ приложения**.

    ![][3]

    Эти значения потребуются при обращении к мобильной службе из кода приложения.

6.  В Visual Studio откройте файл default.js, раскомментируйте следующий код, который определяет переменную **client**, и укажите URL-адрес и ключ приложения мобильной службы в конструкторе **MobileServiceClient** в указанном порядке.

            var client = new WindowsAzure.MobileServiceClient(
                "AppUrl",
                "appKey"
            );

    Код создает экземпляр MobileServiceClient, используемый для доступа к мобильной службе.

7.  Раскомментируйте следующую строку кода:

        var todoTable = client.getTable('TodoItem');

    Этот код создает прокси-объект (**todoTable**) для новой таблицы базы данных.

8.  Замените функцию **InsertTodoItem** следующим кодом:

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            });
        };

    Этот код вставляет новый элемент в таблицу.

9.  Замените функцию **RefreshTodoItems** следующим кодом:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the TodoItems table. 
            todoTable.read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };      

    Это установит привязку к коллекции элементов в таблице todoTable, которая содержит все завершенные элементы, возвращенные из мобильной службы.

10. Замените функцию **UpdateCheckedTodoItem** следующим кодом:

        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
        };

    В результате в мобильную службу будет отправлено обновление элемента.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

## <a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с новой мобильной службой

1.  В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2.  Как и ранее, введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

    В результате в мобильную службу будет отправлен новый элемент в качестве вставки.

3.  На [Портале управления][Портале управления] щелкните **Мобильные службы**, затем щелкните свою мобильную службу.

4.  Откройте вкладку **Данные**, а затем щелкните **Обзор**.

    ![][4]

    Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

5.  В приложении отметьте один из элементов списка, а затем вернитесь на вкладку "Обзор" на портале и нажмите кнопку **Обновить**

    Обратите внимание, что значение выполнения изменилось с **false** на **true**.

6.  В файле проекта default.js замените существующую функцию **RefreshTodoItems** следующим кодом, который будет отфильтровывать завершенные элементы:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

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

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Загрузка проекта приложения для Магазина Windows]: #download-app
  [Создание мобильной службы]: #create-service
  [Добавление таблицы для хранения данных]: #add-table
  [Обновление приложения для использования мобильных служб]: #update-app
  [Тестирование работы приложения с мобильными службами]: #test-app
  [GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkId=262308
  [0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [2]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
  [Портале управления]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-js
  [Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js-vs2012
