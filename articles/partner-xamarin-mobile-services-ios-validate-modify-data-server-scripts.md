<properties urlDisplayName="" pageTitle="Использование серверных скриптов для проверки и изменения данных (Xamarin iOS) | Центр мобильных разработок" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Xamarin iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="donnam" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="donnam" />

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных скриптов часто влияет на клиента, также понадобится обновить приложение iOS, чтобы воспользоваться преимуществом этого нового поведения. Готовый код доступен в примере приложения [ValidateModifyData][GitHub].

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]
3. [Добавление отметки времени при вставке]
4. [Обновление клиента для отображения отметки времени]

В основе этого учебника лежат инструкции и образец приложения из предыдущего учебника [Начало работы с данными]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с данными].  

## <a name="string-length-validation"></a>Добавить проверку

Всегда рекомендуется проверять длину данных, отправляемых пользователям. Вначале регистрируется скрипт для проверки значений длины данных строк, отправляемых мобильной службе, и удаления слишком длинных строк, в данном случае - превышающих 10 символов.

1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение. 

	![][0]

2. Откройте вкладку **Данные**, затем щелкните таблицу **TodoItem**.

   	![][1]

3. Щелкните **Скрипт**, затем выберите операцию **Вставить**.

   	![][2]

4. Замените существующий скрипт следующей функцией, затем нажмите кнопку **Сохранить**.

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

1. Откройте в Xamarin Studio проект, над которым вы работали при прохождении учебника [Приступая к работе с данными].

2. Нажмите кнопку **Пуск**, чтобы построить проект и запустить приложение, затем введите текст длиннее 10 символов и щелкните значок плюса (**+**).

	Обратите внимание, что приложение вызывает необработанную ошибку в результате ответа 400 (Неверный запрос), возвращенного мобильной службой.	

3. В файле TodoService.cs, найдите блоки обработки исключений <code>try/catch</code> в методе **InsertTodoItemAsync**, и замените блок <code>catch</code> следующим:
    
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

	Это приведет к показу всплывающего окна с ошибкой. 

4. Найдите метод **OnAdd** в **TodoListViewController.cs**. Обновите метод и убедитесь, что возвращенный <code>индекс</code> не равен <code>-1</code>, и что он возвращается в блоке обработке исключений в **InsertTodoItemAsync**. В этом случае нет необходимости добавлять новый ряд в <code>TableView</code>.

    if (index != -1) {
        TableView.InsertRows(new [] { NSIndexPath.FromItemSection(index, 0) },
            UITableViewRowAnimation.Top);
        itemText.Text = "";
    }


5. Повторно выполните построение и запустите приложение. 

	![][4]

	Обратите внимание, что ошибка будет обработана и для пользователя отобразится сообщение об ошибке.


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
