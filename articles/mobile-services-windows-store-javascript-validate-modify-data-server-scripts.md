<properties urlDisplayName="Validate Data" pageTitle="Использование серверных скриптов для проверки и изменения данных (JavaScript) | Центр разработчиков для мобильных устройств "metaKeywords ="" description="Узнайте, как проверять и изменять данные, отправленные с помощью серверных сценариев из приложения JavaScript для магазина Windows." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Проверка и изменение данных в мобильных службах с помощью серверных сценариев 

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные сценарии зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные сценарии, которые проверяют и изменяют данные. Так как поведение серверных сценариев часто влияет на клиента, также понадобится обновить приложение Магазина Windows, чтобы воспользоваться преимуществом этого нового поведения.

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]
3. [Добавление отметки времени при вставке]
4. [Обновление клиента для отображения отметки времени]

В основе этого учебника лежат инструкции и образец приложения из предыдущего учебника [Начало работы с данными]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с данными].  

## <a name="string-length-validation"></a>Добавить проверку

Всегда рекомендуется проверять длину данных, отправляемых пользователям. Вначале регистрируется сценарий для проверки значений длины данных строк, отправляемых мобильной службе, и удаления слишком длинных строк, в данном случае - превышающих 10 символов.

1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение. 

   	![][0]

2. Откройте вкладку **Данные**, затем щелкните таблицу **TodoItem**.

   	![][1]

3. Щелкните **Скрипт**, затем выберите операцию **Вставить**.

   	![][2]

4. Замените существующий сценарий следующей функцией, затем нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Этот сценарий проверяет длину свойства **TodoItem.text** и отправляет ответ с сообщением об ошибке, если длина превышает 10 символов. В противном случае вызывается метод **execute** для завершения вставки.

    <div class="dev-callout"> 
	<b>Примечание</b> 
	<p>Можно удалить зарегистрированный скрипт на вкладке <strong>Скрипт</strong>, щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p></div>	

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1.  Откройте в среде Visual Studio 2012 Express для Windows 8 проект, измененный при выполнении учебника [Приступая к работе с данными].

2. Нажмите клавишу **F5**, чтобы запустить приложение, затем введите в поле **Вставить элемент TodoItem** текст, длина которого превышает 10 символов, и нажмите кнопку **Сохранить**.

   	Обратите внимание, что приложение вызывает необработанную ошибку в результате ответа 400 (Неверный запрос), возвращенного мобильной службой.

6. 	Откройте файл default.js, а затем замените существующий метод **InsertTodoItem**следующим:

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            }, function (error) {
                // Create the error message dialog and set its content to the error
                // message contained in the response.
                var msg = new Windows.UI.Popups.MessageDialog(
                    error.request.responseText);
                msg.showAsync();
            });
        };

   	Эта версия метода включает обработку ошибок для исключения, которое отображает ответ на ошибку в диалоговом окне.

## <a name="add-timestamp"></a>Добавление временной метки

В предыдущих задачах проверялась операция вставки, после чего была принята или отклонена. Теперь будет проводиться обновление вставленных данных с использованием серверного скрипта, который добавляет свойство временной метки к объекту перед его вставкой.

<div class="dev-callout"><b>Примечание.</b>
<p>Продемонстрированное здесь свойство отметки времени <b>createdAt</b> теперь является избыточным. Мобильные службы автоматически создают системное свойство <b>__createdAt</b> для каждой таблицы.</p>
</div>

1. На вкладке **Скрипты** на [Портале управления] замените текущий сценарий **Вставка** следующей функцией, затем щелкните ** Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Эта функция улучшает предыдущий скрипт вставки, добавляя к объекту новое свойство - метку времени **createdAt**до того, как он будет вставлен вызовом **request**.**execute**. 

    <div class="dev-callout"><b>Примечание.</b>
	<p>При первом выполнении этого сценария вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>createdAt</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения в Магазине Windows ее следует отключить.</p>
    </div>

2. В Visual Studio нажмите клавишу **F5**, чтобы запустить приложение, затем введите в поле **Вставить элемент TodoItem** текст (менее 10 символов) и нажмите кнопку **Сохранить**.

   	Обратите внимание, что новая отметка времени не отображается в пользовательском интерфейсе приложения.

3. Возвратившись на портал управления, откройте вкладку **Обзор** в таблице **todoitem**.
   
   	Обратите внимание, что теперь появился столбец **createdAt**, а вновь вставленный элемент имеет значение временной метки.
  
Далее необходимо обновить приложение магазина Windows для отображения этого нового столбца.

## <a name="update-client-timestamp"></a>Снова обновите клиент

Клиент мобильной службы будет пропускать любые данные в ответе, которые не сможет сериализовать в свойства согласно заданному типу. Последним шагом является обновление клиента для отображения этих новых данных.

1. В Visual Studio откройте файл default.html, затем добавьте следующий элемент HTML в сетке TemplateItem:
	      
        <div style="-ms-grid-column: 4; -ms-grid-row-align: center; margin-left: 5px" 
            data-win-bind="innerText: createdAt"></div>  

   	Это приведет к отображению нового свойства **createdAt**. 
	
6. Нажмите клавишу **F5**, чтобы запустить приложение. 

   	Обратите внимание, что отметка времени отображается только для вставленных элементов после обновления сценария вставки.

7. В файле default.js замените существующий метод **RefreshTodoItems** следующим кодом:

        var refreshTodoItems = function () {
            // More advanced query that filters out completed items. 
            todoTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            })
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	Этот метод обновляет запрос для исключения тех элементов, которые не имеют значения отметки времени.
	
8. Нажмите клавишу **F5**, чтобы запустить приложение.

   	Обратите внимание, что все элементы, созданные без значения отметки времени, исключаются из пользовательского интерфейса.

Вы завершили работу с учебником данных.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, после прохождения этого учебника, рассмотрите возможность перехода к заключительному учебнику в этом ряду, касающемся данных: [Уточнение запросов с постраничным просмотром].

Серверный сценарий также используются при авторизации пользователей и для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

* [Авторизация пользователей с помощью сценариев]
  <br/>Фильтрация данных с учетом идентификатора пользователя, прошедшего проверку подлинности.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Справочник серверных сценариев мобильных служб]
  <br/>Регистрация и использование серверных сценариев.

<!-- Anchors. -->
[Добавление проверки длины строки]: #string-length-validation
[Обновление клиента для поддержки проверки]: #update-client-validation
[Добавление отметки времени при вставке]: #add-timestamp
[Обновление клиента для отображения отметки времени]: #update-client-timestamp
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Авторизация пользователей с помощью сценариев]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-js
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-js
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[C# и XAML]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet

[Портал управления]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
