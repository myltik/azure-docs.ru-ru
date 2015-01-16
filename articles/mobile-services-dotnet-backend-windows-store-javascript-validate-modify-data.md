<properties urlDisplayName="Validate and Modify Data" pageTitle="Использование серверной части .Net для проверки и изменения данных (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как проверять, изменять и улучшать данные для приложения JavaScript Магазина Windows с помощью мобильных служб Microsoft Azure c серверной частью .NET." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="wesmc" />

# Проверка и изменение данных в мобильных службах с помощью серверной части .NET

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

В этом разделе показано, как использовать код в мобильных службах Azure на основе .NET для проверки и изменения данных. Серверная служба .NET - это HTTP-служба, созданная с помощью платформы Web API. Если вы знакомы с классом `ApiController`, определенным с помощью платформы Web API, то предоставляемый мобильными службами класс `TableController` будет интуитивно понятным. `TableController` является производным от класса `ApiController`. Он предоставляет дополнительные возможности для взаимодействия с таблицей базы данных. Ее можно использовать для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных, что показано в этом учебнике. 

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]
3. [Проверка длины теста]
4. [Добавление метки времени для CompleteDate]
5. [Обновление клиента для отображения CompleteDate]

Этот учебник основан на инструкциях и образце приложения из предыдущего учебника [Приступая к работе] или [Приступая к работе с данными](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе] или [Приступая к работе с данными](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/).  

## <a name="string-length-validation"></a>Добавление кода проверки в мобильную службу

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба настроена для проверки данных и отправки сообщений об ошибках при недопустимой длине текста, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки. Ошибка будет перехвачена из вызова клиентского приложения, направленного к `IMobileServiceTable<TodoItem].InsertAsync()`.

1. В обозревателе решений в среде Visual Studio откройте проект клиентского приложения на JavaScript и разверните папку **js**. Затем откройте файл default.js.

2. В файле default.js замените существующую функцию **insertTodoItem** следующим определением функции:


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

1. В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект клиентского приложения на JavaScript, затем выберите **Отладка**, **Запустить новый экземпляр**.

2. Введите текст для нового элемента списка дел длиной более 10 символов, затем нажмите кнопку **Сохранить**.

    ![][1]

3. В ответ на ввод недопустимого текста появится следующее диалоговое окно.

    ![][2]

## <a name="add-timestamp"></a>Добавление метки времени для CompleteDate


[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Обновление клиента для отображения completeDate

Последний шаг - обновление клиента с целью отображения новых данных **completeDate**. 


1. В обозревателе решений в Visual Studio откройте проект клиентского приложения JavaScript и откройте файл default.html. Замените элемент тега `div` шаблона привязки на следующее определение. Затем сохраните файл. При этом будет добавлен тег `div` со свойством innerText, привязанным к **completeDate**.
	      
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



2. В файле default.js удалите функцию `.Where` в существующей функции **refreshTodoItems**, чтобы добавить в результаты завершенные элементы todoitem.

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };


3. В файле default.js обновите функцию **updateCheckedTodoItem**, как показано ниже, чтобы элементы обновлялись после изменения, а завершенные элементы не удалялись из списка. Затем сохраните файл.	

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };


4. В окне обозревателя решений в Visual Studio щелкните правой кнопкой мыши **Решение** и выберите команду **Перестроить решение** для перестройки клиента и серверной службы .NET. Убедитесь, что оба проекта были построены без ошибок.

    ![][3]
	
5. Нажмите клавишу **F5**, чтобы запустить клиентское приложение и службу локально. Добавьте новые элементы и отметьте некоторые как завершенные, чтобы убедиться, что метка времени **CompleteDate** обновляется.

    ![][4]

6. В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект службы todolist и выберите **Опубликовать**. Опубликуйте службу .NET в Microsoft Azure с помощью файла настроек публикации, загруженного с портала Azure.

7. Обновите файл default.js для проекта клиентского приложения, раскомментировав подключение к адресу мобильной службы. Проверьте приложение на серверной части .NET, размещенной в вашей учетной записи Azure.




## <a name="next-steps"> </a>Дальнейшие действия

Теперь, после прохождения этого учебника, рассмотрите возможность перехода к заключительному учебнику в этом ряду, касающемся данных: [Уточнение запросов с постраничным просмотром].

Серверный скрипт также используются при авторизации пользователей и для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

* [Авторизация пользователей на стороне службы]
  <br/>Фильтрация данных с учетом идентификатора пользователя, прошедшего проверку подлинности.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Использование мобильных служб с .NET.

<!-- Anchors. -->
[Добавление проверки длины строки]: #string-length-validation
[Обновление клиента с целью поддержки проверки]: #update-client-validation
[Проверка длины теста]: #test-length-validation
[Добавление поля метки времени для CompleteDate]: #add-timestamp
[Обновление клиента с целью отображения CompleteDate]: #update-client-timestamp
[Следующие шаги]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Авторизация пользователей на стороне службы]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
[Уточнение запросов посредством разбиения на страницы]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
[Приступая к работе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Знакомство с механизмом проверки подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Портал управления]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
[Концептуальный справочник по мобильным службам на платформе .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
