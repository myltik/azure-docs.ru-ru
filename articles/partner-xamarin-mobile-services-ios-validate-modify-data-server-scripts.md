<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-Xamarin-iOS" urlDisplayName="" pageTitle="Использование серверных скриптов для проверки данных (Xamarin iOS) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как проверять и изменять данные с помощью серверных скриптов в вашем приложении Xamarin iOS." metaCanonical="" services="" documentationCenter="Mobile" title="Проверка и изменение данных в мобильных службах путем использования серверных скриптов" authors="" solutions="" manager="" editor="" />


# Проверка и изменение данных в мобильных службах с помощью серверных скриптов
<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>


В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных скриптов часто влияет на клиента, также понадобится обновить приложение iOS, чтобы воспользоваться преимуществом этого нового поведения. Готовый код доступен в примере [ValidateModifyData app][GitHub].

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]
3. [Добавление отметки времени при вставке]
4. [Обновление клиента для отображения отметки времени]

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с данными].  

## <a name="string-length-validation"></a>Добавление проверки

Рекомендуется всегда проверять длину данных, предоставляемых пользователями. Сначала необходимо зарегистрировать скрипт, который проверяет длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинны строки; в этом случае отклоняются строки длиной более 10 знаков.

1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение. 

	![][0]

2. Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

   	![][1]

3. Щелкните элемент **Скрипт** и выберите операцию **Вставить**.

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
	<b>Примечание</b> 
	<p>Можно удалить зарегистрированный скрипт на вкладке <strong>Скрипт</strong>, щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p></div>

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1. В Xamarin Studio откройте проект, измененный после изучения учебника [Приступая к работе с данными].

2. Нажмите кнопку **Запуск**, чтобы построить проект и запустить приложение, а затем введите текст длиннее 10 символов в текстовом поле и нажмите значок (**+**).

	Обратите внимание, что приложение вызывает необработанную ошибку в результате ответа 400 (Неверный запрос), возвращенного мобильной службой.	

3. В файле TodoService.cs найдите текущее исключение обработки <code>try/catch</code> в методе **InsertTodoItemAsync** и замените <code>catch</code> на:
    
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

4. Найдите метод **OnAdd** в **TodoListViewController.cs**. Обновите метод и убедитесь в том, что возвращаемый <code>index</code> не совпадает со значением <code>-1</code>, возвращаемым при обработке исключений в **InsertTodoItemAsync**. В этом случае не нужно добавить новую строку для <code>TableView</code>.

    if (index != -1) {
        TableView.InsertRows(new [] { NSIndexPath.FromItemSection(index, 0) },
            UITableViewRowAnimation.Top);
        itemText.Text = "";
    }


5. Повторно выполните построение и запустите приложение. 

	![][4]

	Обратите внимание, что ошибка будет обработана и для пользователя отобразится сообщение об ошибке.


## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы завершили работу с этим учебником, проработайте последний учебник в серии работы с данными: [Уточнение запросов посредством разбиения по страницам].

Серверные скрипты используются также при авторизации пользователей для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

* [Авторизация пользователей с помощью скриптов]
  <br/>Сведения о фильтрации данных на основе идентификатора пользователя, прошедшего проверку.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о регистрации и использовании серверных скриптов.

<!-- Anchors. -->
[Добавление проверки длины строки]: #string-length-validation
[Обновление клиента для поддержки проверки]: #update-client-validation
[Добавление отметки времени при вставке]: #add-timestamp
[Обновление клиента для отображения отметки времени]: #update-client-timestamp
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-ios
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Портал управления]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330

