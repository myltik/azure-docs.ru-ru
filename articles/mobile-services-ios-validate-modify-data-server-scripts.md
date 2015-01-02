<properties urlDisplayName="Validate Data" pageTitle="Использование скриптов на сервере для проверки и изменения данных (iOS) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных скриптов часто влияет на клиента, также понадобится обновить приложение iOS, чтобы воспользоваться преимуществом этого нового поведения.

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]


В основе этого учебника лежат инструкции и образец приложения из предыдущего учебника [Начало работы с данными]. Прежде чем начать работу с этим учебником, необходимо пройти [Начало работы с данными].  

## <a name="string-length-validation"></a>Добавление проверки

Рекомендуется всегда проверять длину данных, предоставляемых пользователями. Сначала необходимо зарегистрировать скрипт, который проверяет длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинны строки; в этом случае отклоняются строки длиной более 10 знаков.

1. Войдите в [портал управления Azure], щелкните **Мобильные службы**, а затем выберите свое приложение.

   	![][0]

2. Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

   	![][1]

3.  Щелкните элемент **Script** и выберите операцию **Insert**.

   	![][2]

4. Замените имеющийся скрип следующей функцией и нажмите кнопку **Сохранить**.

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
	<p>Можно удалить зарегистрированный скрипт на вкладке <strong>Скрипт</strong>, щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p></div>

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1. В Xcode откройте проект, измененный при завершении учебника [Приступая к работе с данными].

2. Нажмите кнопку **Запуск** ("Command" + R), чтобы построить проект и запустить приложение, а затем введите текст длиннее 10 символов в текстовом поле и нажмите значок (**+**).

   	Обратите внимание, что приложение выдает необработанную ошибку в результате ответа 400 (Неверный запрос), возвращенного мобильной службой.

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

   	При этом ошибка регистрируется в окне вывода и это окно отображается для пользователя.

4. Повторно выполните построение и запустите приложение.

   	![][4]

  	Обратите внимание, что ошибка обработана и для пользователя отобразится сообщение об ошибке.

## <a name="add-timestamp"></a>Добавление отметки времени

Предыдущие задачи проверили вставку и приняли или отклонили ее. Теперь можно обновить вставленные данные путем использования серверного скрипта, который добавляет свойство отметки времени в объект до его вставки.

1. На вкладке **Скрипты** [портала управления] замените текущий скрипт **Insert** на следующую функцию и нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Эта функция дополняет предыдущий скрипт вставки, добавляя новое свойство отметки **createdAt**времени к объекту перед его вставкой в ходе вызова **request**.**execute**.

    <div class="dev-callout"><b>Примечание.</b>
	<p>При первом выполнении этого сценария вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>createdAt</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения ее следует отключить.</p>
    </div>

2. В Visual Studio нажмите клавишу **F5**, чтобы запустить приложение, затем введите текст (короче 10 символов) в поле **Insert a TodoItem** и нажмите кнопку **Save**.

   	Обратите внимание, что новая отметка времени не отображается в пользовательском интерфейсе приложения.

3. На портале управления щелкните вкладку **Обзор** в таблице **todoitem**.

   	Обратите внимание, что теперь есть столбец **createdAt**, и новый элемент имеет отметку времени.

Далее необходимо обновить приложение iOS для отображения этого нового столбца.

## <a name="update-client-timestamp"></a>Снова обновите клиент

Клиент мобильной службы игнорирует любые данные в ответе, которые не могут быть сериализованы в свойства определенного типа. Последним шагом является обновление клиента для отображения новых данных.

1. В Visual Studio откройте файл MainPage.xaml.cs, а затем замените существующий класс **TodoItem** следующим определением:

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

    Это новое определение класса включает новое свойство отметки времени в качестве типа даты и времени, который поддерживает значение Null.

    <div class="dev-callout"><b>Примечание.</b>
	<p><strong>DataMemberAttribute</strong> заставляет клиент сопоставить новое свойство <strong>CreatedAt</strong> в приложении со столбцом <strong>createdAt</strong>, определенным в таблице TodoItem, который записан в другом регистре. С помощью этого атрибута ваше приложение может получить имена свойств для объектов, которые отличаются от имен столбцов в базе данных SQL. Без этого атрибута ошибка возникает из-за различия в буквенном регистре.</p>
    </div>

2. Добавьте следующий XAML-элемент сразу после элемента **CheckBoxComplete** в файле MainPage.xaml:

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	При этом новое свойство **CreatedAt** будет выводиться в текстовом поле.

3. Нажмите клавишу **F5**, чтобы запустить приложение.

   Обратите внимание, что отметка времени отображается только для вставленных элементов после обновления скрипта вставки.

4. Замените существующий метод **RefreshTodoItems** следующим кодом:

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

5. Нажмите клавишу **F5**, чтобы запустить приложение.

   	Обратите внимание, что все элементы, созданные без значения отметки времени, исключаются из пользовательского интерфейса.

Вы завершили работу с учебником данных.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, после прохождения этого учебника, рассмотрите возможность перехода к заключительному учебнику в этом ряду, касающемся данных: [Уточнение запросов с постраничным просмотром].

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

[Портал управления]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
