<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-javascript" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Javascript Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Проверка и изменение данных в мобильных службах с помощью серверной части .NET

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title="Магазин Windows C#">Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title="Магазин Windows JavaScript" class="current">Магазин Windows JavaScript</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>
<div class="dev-center-tutorial-subselector">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title="Сервер .NET" class="current">Сервер .NET</a> | 
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/"  title="Сервер JavaScript">Сервер JavaScript</a>
</div>


В этом разделе показано, как использовать код в мобильных службах Azure на основе .NET для проверки и изменения данных. Серверная служба .NET — это HTTP-служба, созданная с помощью платформы Web API. Если вам знаком класс `ApiController`, определенный с помощью платформы веб-API, класс `TableController`, предоставляемый мобильными службами, будет вам интуитивно понятен. `TableController` является производным от класса `ApiController` и предоставляет дополнительные возможности для взаимодействия с таблицей базы данных. Его можно использовать для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных, что показано в этом учебнике.

В этом учебнике рассматриваются следующие основные действия:

1.  [Добавление проверки длины строки][Добавление проверки длины строки]
2.  [Обновление клиента для поддержки проверки][Обновление клиента для поддержки проверки]
3.  [Проверка длины теста][Проверка длины теста]
4.  [Добавление метки времени для CompleteDate][Добавление метки времени для CompleteDate]
5.  [Обновление клиента для отображения CompleteDate][Обновление клиента для отображения CompleteDate]

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе][Приступая к работе] или [Приступая к работе с данными][Приступая к работе с данными]. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе][Приступая к работе] или [Приступая к работе с данными][Приступая к работе с данными].

## <a name="string-length-validation"></a>Добавление кода проверки в мобильную службу

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба настроена для проверки данных и отправки сообщений об ошибках при недопустимой длине текста, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки. Ошибка будет перехвачена из вызова клиентского приложения в `IMobileServiceTable<TodoItem].InsertAsync()`.

1.  В окне обозревателя решений в Visual Studio откройте проект клиентского приложения на JavaScript и разверните папку **js**. Затем откройте файл default.js.

2.  В файле default.js замените существующую функцию **insertTodoItem** на следующее определение функции:

        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
            todoTable.insert(todoItem)
                .then(function (item) {
                  todoItems.push(item);
                },
                function (error) {
                  var msgDialog =
                    new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                    error.request.statusText + "(" + error.request.status + ")");
                  msgDialog.showAsync();
                });
        };

    Эта версия функции содержит обработку ошибок и отображает `MessageDialog` с сообщением об ошибке из ответа, текст состояния и код состояния.

## <a name="test-length-validation"></a>Проверка длины теста

1.  В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект клиентского приложения на JavaScript и щелкните **Отладка**, затем **Запустить новый экземпляр**.

2.  Введите текст для нового элемента списка дел длиной более 10 символов и нажмите кнопку **Сохранить**.

    ![][0]

3.  В ответ на ввод недопустимого текста появится следующее диалоговое окно.

    ![][1]

## <a name="add-timestamp"></a>Добавление поля метки времени для CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Изменение клиента для отображения completeDate

Последним шагом является обновление клиента для отображения новых данных **CompleteDate**.

1.  В обозревателе решений для Visual Studio, в проекте клиентского приложения на JavaScript откройте файл default.html. Замените элемент тега `div` шаблона привязки на следующее определение. Затем сохраните файл. При этом будет добавлен тег `div` со свойством innerText, привязанным к **completeDate**.

        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: 3">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; 
              dataContext: this" />
            <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px" 
              data-win-bind="innerText: text">
            </div>
            <div style="-ms-grid-column: 3; -ms-grid-row-align: center; margin-left: 10px" 
              data-win-bind="innerText: completeDate">
            </div>
          </div>
        </div>

2.  В файле default.js удалите функцию с предложением `.Where` в существующей функции **refreshTodoItems**, чтобы включить завершенные элементы todoitem в результаты.

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };

3.  В файле default.js обновите функцию **updateCheckedTodoItem**, как показано ниже, чтобы элементы обновлялись после изменения, а завершенные элементы не удалялись из списка. Затем сохраните файл.

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };

4.  В окне обозревателя решений в Visual Studio щелкните правой кнопкой мыши **Решение** и выберите пункт **Перестроить решение**, чтобы повторить сборку клиента и серверной службы .NET. Убедитесь, что построение выполнено без ошибок.

    ![][2]

5.  Нажмите клавишу **F5**, чтобы локально запустить клиентское приложение и службу. Добавьте новые элементы и отметьте некоторые элементы как завершенные, чтобы убедиться, что временная метка **CompleteDate** обновляется.

    ![][3]

6.  В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект службы todolist и выберите **Опубликовать**. Опубликуйте службу .NET в Microsoft Azure с помощью файла настроек публикации, загруженного с портала Azure.

7.  Обновите файл default.js для проекта клиентского приложения, раскомментировав подключение к адресу мобильной службы. Проверьте приложения со службой .NET, размещенной в учетной записи Azure.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы завершили работу с этим учебником, проработайте последний учебник в серии работы с данными: [Уточнение запросов c разбиением по страницам][Уточнение запросов c разбиением по страницам].

Серверные скрипты используются также при авторизации пользователей для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

-   [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей мобильных служб на стороне службы]

    Узнайте, как фильтровать данные на основании идентификатора аутентифицированного пользователя.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]

    Дополнительные сведения об использовании мобильных служб в .NET.



  [Добавление проверки длины строки]: #string-length-validation
  [Обновление клиента для поддержки проверки]: #update-client-validation
  [Проверка длины теста]: #test-length-validation
  [Добавление метки времени для CompleteDate]: #add-timestamp
  [Обновление клиента для отображения CompleteDate]: #update-client-timestamp
  [Приступая к работе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png
  [Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Авторизация пользователей мобильных служб на стороне службы]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
