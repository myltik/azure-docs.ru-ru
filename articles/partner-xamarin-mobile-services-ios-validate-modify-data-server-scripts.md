<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-Xamarin-iOS" urlDisplayName="" pageTitle="Use server scripts to validate and modify data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Xamarin iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="donnam" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash;JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных скриптов часто влияет на клиента, также понадобится обновить приложение iOS, чтобы воспользоваться преимуществом этого нового поведения. Готовый код доступен в примере [ValidateModifyData app][ValidateModifyData app].

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
        if (item.text.length \> 10) {
        request.respond(statusCodes.BAD\_REQUEST, 'Text length must be 10 characters or less.');
        } else {
        request.execute();
        }
        }

    Этот скрипт проверяет длину свойства **text** и отправляет ответ с сообщением об ошибке, если длина превышает 10 символов. В противном случае вызывается метод **execute** для завершения вставки.

    <div class="dev-callout"> 
<b>Примечание.</b> 
<p>Можно удалить зарегистрированный сценарий на вкладке <strong>Сценарий</strong>, щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p></div>

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1.  Откройте в Xamarin Studio проект, измененный при завершении изучения учебника [Приступая к работе с данными][Приступая к работе с данными].

2.  Нажмите кнопку **Запуск**, чтобы выполнить сборку проекта и запустить приложение, а затем введите в текстовое поле текст длиной более 10 символов и щелкните значок «плюс» (**+**).

    Обратите внимание, что приложение вызывает необработанную ошибку в результате ответа 400 (Неверный запрос), возвращенного мобильной службой.

3.  В файле TodoService.cs найдите текущий оператор обработки исключений `try/catch` в методе **InsertTodoItemAsync** и замените `catch` на:

        catch (Exception ex) {
        var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException);
        Console.WriteLine(exDetail.Message);
        UIAlertView alert = new UIAlertView() { 
                Title = "Error", 
                Message = exDetail.Message
        } ;
        alert.AddButton("Ok");
        alert.Show();
        return -1;
        }

    Появится всплывающее окно, которое отображает сообщение об ошибке для пользователя.

4.  Найдите метод **OnAdd** в файле **TodoListViewController.cs**. Обновите метод и убедитесь в том, что возвращаемый `index` не совпадает со значением `-1`, возвращаемым при обработке исключений в **InsertTodoItemAsync**. В этом случае не нужно добавить новую строку для `TableView`.

        if (index != -1) {
        TableView.InsertRows(new [] { NSIndexPath.FromItemSection(index, 0) },
        UITableViewRowAnimation.Top);
        itemText.Text = "";
        }

5.  Повторно выполните сборку и запустите приложение.

    ![][3]

    Обратите внимание, что ошибка будет обработана и для пользователя отобразится сообщение об ошибке.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы завершили работу с этим учебником, проработайте последний учебник в серии работы с данными: [Уточнение запросов c разбиением по страницам][Уточнение запросов c разбиением по страницам].

Серверные скрипты используются также при авторизации пользователей для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

-   [Авторизация пользователей с помощью сценариев][Авторизация пользователей с помощью сценариев]
    Узнайте, как фильтровать данные на основании идентификатора аутентифицированного пользователя.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
    Узнайте больше о регистрации и использовании серверных сценариев.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Магазин Windows — C#]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Магазин Windows — C#"
  [Магазин Windows —JavaScript]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js "Магазин Windows — JavaScript"
  [Windows Phone]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [ValidateModifyData app]: http://go.microsoft.com/fwlink/p/?LinkId=331330
  [Добавление проверки длины строки]: #string-length-validation
  [Обновление клиента для поддержки проверки]: #update-client-validation
  [Добавление отметки времени при вставке]: #add-timestamp
  [Обновление клиента для отображения отметки времени]: #update-client-timestamp
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [3]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
  [Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [Авторизация пользователей с помощью сценариев]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
