<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-wp8" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Phone 8 app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone" class="current">Windows Phone</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Сервер .NET">Сервер .NET</a> | 
    <a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8"  title="Сервер JavaScript" class="current">Сервер JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных сценариев часто влияет на клиент, вам также понадобится обновить приложение для Windows Phone 8, чтобы воспользоваться преимуществом новых возможностей поведения.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-validate-modify-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">11:36:00</span></div>

</div>

В этом учебнике рассматриваются следующие основные действия:

1.  [Добавление проверки длины строки][Добавление проверки длины строки]
2.  [Обновление клиента для поддержки проверки][Обновление клиента для поддержки проверки]
3.  [Добавление отметки времени при вставке][Добавление отметки времени при вставке]
4.  [Обновление клиента для отображения отметки времени][Обновление клиента для отображения отметки времени]

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными][Приступая к работе с данными]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с данными][Приступая к работе с данными].

## <a name="string-length-validation"></a>Добавление проверки

Рекомендуется всегда проверять длину данных, предоставляемых пользователями. Сначала необходимо зарегистрировать скрипт, который проверяет длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинны строки; в этом случае отклоняются строки длиной более 10 знаков.

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

    ![][]

2.  Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

    ![][1]

3.  Щелкните **Сценарий** и выберите операцию **Вставить**.

    ![][2]

4.  Замените имеющийся сценарий следующей функцией и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Этот скрипт проверяет длину свойства **TodoItem.text** и отправляет ответ с сообщением об ошибке, если длина превышает 10 символов. В противном случае вызывается метод **execute** для завершения вставки.

    <div class="dev-callout"> 
<b>Примечание.</b> 
<p>Можно удалить зарегистрированный сценарий на вкладке <strong>Сценарий</strong>, щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p></div>

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1.  Откройте в Visual Studio 2012 Express для Windows Phone проект, измененный во время работы с учебником [Приступая к работе с данными][Приступая к работе с данными].

2.  Нажмите клавишу **F5**, чтобы запустить приложение, введите в текстовом поле какой-либо текст из более чем 10 символов и нажмите кнопку **Сохранить**.

    Обратите внимание, что приложение вызывает необработанное исключение **MobileServiceInvalidOperationException** в результате ответа 400 (Неверный запрос), возвращенного мобильной службой.

3.  Откройте файл MainPage.xaml.cs, а затем замените существующий метод **InsertTodoItem** следующим:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. 
            // When the operation completes and Mobile Services has 
            // assigned an Id, the item is added to the collection.
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                MessageBox.Show(e.Message,
                    string.Format("{0} (HTTP {1})",
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode), 
                    MessageBoxButton.OK);
            }
        }

    Эта версия метода включает обработку ошибок для исключения **MobileServiceInvalidOperationException** , которое отображает ответ на ошибку в окне сообщения.

## <a name="add-timestamp"></a>Добавление отметки времени

Предыдущие задачи проверили вставку и приняли или отклонили ее. Теперь можно обновить вставленные данные путем использования серверного скрипта, который добавляет свойство отметки времени в объект до его вставки.

<div class="dev-callout"><b>Примечание.</b>
<p>Продемонстрированное здесь свойство метки времени <b>createdAt</b> теперь является избыточным. Мобильные службы автоматически создают системное свойство <b>__createdAt</b> для каждой таблицы. Чтобы использовать это свойство системы в своем приложении, просто добавьте следующий элемент в класс TodoItem.</p>
<pre><code>
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
</code></pre>
</div>

1.  На вкладке **Сценарии** [портала управления][портал управления Azure] замените текущий сценарий **вставки** следующей функцией, затем нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Эта функция расширяет предыдущий скрипт вставки путем добавления нового свойства отметки времени **createdAt** в объект до его вставки путем вызова **request**.**execute**.

    <div class="dev-callout"><b>Примечание.</b>
<p>При первом выполнении этого сценария вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>createdAt</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения в Магазине Windows Phone ее следует отключить.</p>
</div>

2.  В Visual Studio нажмите клавишу **F5**, чтобы запустить приложение, введите в текстовом поле какой-либо текст из менее чем 10 символов и нажмите кнопку **Сохранить**.

    Обратите внимание, что новая отметка времени не отображается в пользовательском интерфейсе приложения.

3.  На портале управления щелкните вкладку **Обзор** в таблице **todoitem**.

    Обратите внимание на появление столбца **createdAt** и на то, что новый вставленный элемент имеет значение отметки времени.

Далее необходимо обновить приложение Windows Phone для отображения этого нового столбца.

## <a name="update-client-timestamp"></a>Повторное обновление клиента

Клиент мобильной службы игнорирует любые данные в ответе, которые не могут быть сериализованы в свойства определенного типа. Последним шагом является обновление клиента для отображения новых данных.

1.  В Visual Studio откройте файл MainPage.xaml.cs и замените существующий класс **TodoItem** на следующее определение:

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
            [JsonProperty(PropertyName = "createdAt")]
            public DateTime? CreatedAt { get; set; }
        }

    Это новое определение класса включает новое свойство отметки времени в качестве типа даты и времени, который поддерживает значение NULL.

    <div class="dev-callout"><b>Примечание.</b>
<p>Атрибут <strong>DataMemberAttribute</strong> предлагает клиенту сопоставить новое свойство <strong>CreatedAt</strong> в приложении со столбцом <strong>createdAt</strong>, определенным в таблице TodoItem, в которой используется другой регистр. С помощью этого атрибута ваше приложение может получить имена свойств для объектов, которые отличаются от имен столбцов в базе данных SQL. Без этого атрибута ошибка возникает из-за различия в буквенном регистре.</p>
</div>

2.  Добавьте следующий элемент XAML прямо под элементом **CheckBoxComplete** в файле MainPage.xaml:

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

    Это приведет к отображению нового свойства **CreatedAt** в текстовом поле.

3.  Нажмите клавишу **F5**, чтобы запустить приложение.

    Обратите внимание, что отметка времени отображается только для вставленных элементов после обновления скрипта вставки.

4.  Замените существующий метод **RefreshTodoItems** на следующий код:

        private async void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionAsync();
            ListItems.ItemsSource = items;
        }

    Этот метод обновляет запрос для исключения тех элементов, которые не имеют значения отметки времени.

5.  Нажмите клавишу **F5**, чтобы запустить приложение.

    Обратите внимание, что все элементы, созданные без значения отметки времени, исключаются из пользовательского интерфейса.

Вы завершили работу с учебником данных.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы завершили работу с этим учебником, проработайте последний учебник в серии работы с данными:

Серверные скрипты используются также при авторизации пользователей для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках: [Уточнение запросов c разбиением по страницам][Уточнение запросов c разбиением по страницам].

-   [Авторизация пользователей с помощью сценариев][Авторизация пользователей с помощью сценариев]
    <br/>Узнайте, как фильтровать данные на основании идентификатора аутентифицированного пользователя.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    <br/>Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
    <br/>Узнайте больше о регистрации и использовании серверных сценариев.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Магазин Windows C#]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Магазин Windows C#"
  [Магазин Windows JavaScript]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js "Магазин Windows JavaScript"
  [Windows Phone]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Сервер .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Сервер .NET"
  [Сервер JavaScript]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Сервер JavaScript"
  [Просмотр учебника]: http://go.microsoft.com/fwlink/?LinkId=298629
  [Добавление проверки длины строки]: #string-length-validation
  [Обновление клиента для поддержки проверки]: #update-client-validation
  [Добавление отметки времени при вставке]: #add-timestamp
  [Обновление клиента для отображения отметки времени]: #update-client-timestamp
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8
  [Авторизация пользователей с помощью сценариев]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-wp8
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
