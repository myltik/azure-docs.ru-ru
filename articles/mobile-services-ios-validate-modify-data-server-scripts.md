<properties urlDisplayName="Validate Data" pageTitle="Использование скриптов на сервере для проверки и изменения данных (iOS) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных скриптов часто влияет на клиента, также понадобится обновить приложение iOS, чтобы воспользоваться преимуществом этого нового поведения.

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]


В основе этого учебника лежат инструкции и образец приложения из предыдущего учебника [Приступая к работе с данными]. Прежде чем начать работу с этим учебником, необходимо пройти [Приступая к работе с данными].  

## <a name="string-length-validation"></a>Добавление проверки

Рекомендуется всегда проверять длину данных, предоставляемых пользователями. Сначала необходимо зарегистрировать скрипт, который проверяет длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинны строки; в этом случае отклоняются строки длиной более 10 знаков.

1. Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

   	![][0]

2. Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

   	![][1]

3. Щелкните **Сценарий** и выберите операцию **Вставить**.

   	![][2]

4. Замените имеющийся сценарий следующей функцией и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Этот скрипт проверяет длину свойства **text** и отправляет ответ с сообщением об ошибке, если длина превышает 10 символов. В противном случае вызывается метод **execute** для завершения вставки.

    <div class="dev-callout">
	<b>Примечание.</b>
	<p>Можно удалить зарегистрированный сценарий на вкладке <strong>Сценарий</strong>, щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p>.</p></div>

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1. В Xcode откройте проект, измененный после завершения учебника [Приступая к работе с данными][Приступая к работе с данными].

2. Нажмите кнопку **Запуск** (или клавиши Command+R), чтобы выполнить сборку проекта и запустить приложение, а затем введите в текстовом поле текст длиннее 10 символов и нажмите значок (**+**).

   	Обратите внимание, что приложение вызывает необработанную ошибку в результате ответа 400 (Неверный запрос), возвращенного мобильной службой.

3. В файле QSTodoService.m найдите следующую строку кода в методе **addItem**:

        [self logErrorIfNotNil:error];

   	После этой строки кода замените оставшуюся части блока выполнения следующим кодом:

        BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

        // detect text validation error from service.
        if (goodRequest) // The service responded appropriately
        {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:result atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }
        else{

            // if there's an error that came from the service
            // log it, and popup up the returned string.
            if (error && error.code == MSErrorMessageErrorCode) {
                NSLog(@"ERROR %@", error);
                UIAlertView *av =
                [[UIAlertView alloc]
                 initWithTitle:@"Request Failed"
                 message:error.localizedDescription
                 delegate:nil
                 cancelButtonTitle:@"OK"
                 otherButtonTitles:nil
                 ];
                [av show];
            }
        }

   	При этом ошибка записывается в окне вывода, оно отображается для пользователя.

4. Повторно выполните сборку и запустите приложение.

   	![][4]

  	Обратите внимание, что ошибка будет обработана и для пользователя отобразится сообщение об ошибке.

<!--## <a name="add-timestamp"></a>Add a timestamp

The previous tasks validated an insert and either accepted or rejected it. Now, you will update inserted data by using a server script that adds a timestamp property to the object before it gets inserted.

1. In the **Scripts** tab in the [Management Portal], replace the current **Insert** script with the following function, and then click **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    This function augments the previous insert script by adding a new **createdAt** timestamp property to the object before it gets inserted by the call to **request**.**execute**.

    <div class="dev-callout"><b>Note</b>
	<p>Dynamic schema must be enabled the first time that this insert script runs. With dynamic schema enabled, Mobile Services automatically adds the <strong>createdAt</strong> column to the <strong>TodoItem</strong> table on the first execution. Dynamic schema is enabled by default for a new mobile service, and it should be disabled before the app is published.</p>
    </div>

2. In Visual Studio, press the **F5** key to run the app, then type text (shorter than 10 characters) in **Insert a TodoItem** and click **Save**.

   	Notice that the new timestamp does not appear in the app UI.

3. Back in the Management Portal, click the **Browse** tab in the **todoitem** table.

   	Notice that there is now a **createdAt** column, and the new inserted item has a timestamp value.

Next, you need to update the iOS app to display this new column.

## <a name="update-client-timestamp"></a>Update the client again

The Mobile Service client will ignore any data in a response that it cannot serialize into properties on the defined type. The final step is to update the client to display this new data.

1. In Visual Studio, open the file MainPage.xaml.cs, then replace the existing **TodoItem** class with the following definition:

	    public class TodoItem
	    {
	        public int Id { get; set; }

            [DataMember(Name="text")]
	        public string Text { get; set; }

            [DataMember(Name="complete")]
	        public bool Complete { get; set; }

            [DataMember(Name="createdAt")]
	        public DateTime? CreatedAt { get; set; }
	    }

    This new class definition includes the new timestamp property, as a nullable DateTime type.

    <div class="dev-callout"><b>Note</b>
	<p>The <strong>DataMemberAttribute</strong> tells the client to map the new <strong>CreatedAt</strong> property in the app to the <strong>createdAt</strong> column defined in the TodoItem table, which has a different casing. By using this attribute, your app can have property names on objects that differ from column names in the SQL Database. Without this attribute, an error would occur because of the casing differences.</p>
    </div>

5. Add the following XAML element just below the **CheckBoxComplete** element in the MainPage.xaml file:

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	Это приведет к отображению нового свойства **CreatedAt** в текстовом поле.

6. Нажмите клавишу **F5**, чтобы запустить приложение.

   Обратите внимание, что отметка времени отображается только для вставленных элементов после обновления скрипта вставки.

7. Замените существующий метод **RefreshTodoItems** следующим кодом:

        private void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and
            // items without a timestamp.
            items = todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionView();

            ListItems.ItemsSource = items;
        }

   	Этот метод обновляет запрос для исключения тех элементов, которые не имеют значения отметки времени.

8. Нажмите клавишу **F5**, чтобы запустить приложение.

   	Обратите внимание, что все элементы, созданные без значения отметки времени, исключаются из пользовательского интерфейса.

Работа с учебником по данным завершена.-->

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, после прохождения этого учебника, рассмотрите возможность перехода к заключительному учебнику в этом ряду, касающемся данных: [Уточнение запросов с постраничным просмотром][Уточнение запросов посредством разбиения по страницам].

Серверный скрипт также используются при авторизации пользователей и для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

* [Авторизация пользователей с помощью скриптов]
  <br/>Фильтрация данных с учетом идентификатора пользователя, прошедшего проверку подлинности.

* [Приступая к работе с push-уведомлениями]
  <br/>Отправка очень простого push-уведомления в приложение.

* [Справочник серверных скриптов мобильных служб]
  <br/>Регистрация и использование серверных скриптов.

<!-- Anchors. -->
[Добавление проверки длины строки]: #string-length-validation
[Обновление клиента для поддержки проверки]: #update-client-validation
[Добавление отметки времени при вставке]: #add-timestamp
[Обновление клиента для отображения отметки времени]: #update-client-timestamp
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios

[портал управления Azure]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
