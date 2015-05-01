<properties
	pageTitle="Использование серверных скриптов для проверки и изменения данных (Xamarin iOS) | Центр мобильных разработок"
	description="Узнайте, как проверять и изменять данные, отправленные с помощью серверных скриптов из приложения Xamarin для iOS."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/26/2014"
	ms.author="donnam"/>

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных скриптов часто влияет на клиента, также понадобится обновить приложение iOS, чтобы воспользоваться преимуществом этого нового поведения. Готовый код доступен в примере приложения [ValidateModifyData][GitHub].

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]
3. [Добавление метки времени при вставке]
4. [Обновление клиента для отображения метки времени]

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными]. Перед началом работы с учебником пройдите задания учебника [Начало работы с данными].  

## <a name="string-length-validation"></a>Добавление проверки

Рекомендуется всегда проверять длину данных, предоставляемых пользователями. Сначала необходимо зарегистрировать скрипт, который проверяет длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинные строки; в этом случае отклоняются строки длиной более 10 знаков.

1. Войдите на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

	![][0]

2. На вкладке **Данные** щелкните таблицу **TodoItem**.

   ![][1]

3. Выберите элемент **Скрипт**, а затем - операцию **Вставить**.

   ![][2]

4. Замените существующий скрипт указанной ниже функцией и нажмите кнопку **Сохранить**.

				function insert(item, user, request) {
        	if (item.text.length > 10) {
            request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
          } else {
            request.execute();
          }
        }

    Этот скрипт проверяет длину свойства **text** и отправляет ответ с сообщением об ошибке, если длина превышает 10 символов. В противном случае вызывается метод **execute** для завершения вставки.

    > [AZURE.NOTE] Вы можете удалить зарегистрированный скрипт на вкладке **Скрипт**, щелкнув **Очистить**, а затем - **Сохранить**.

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1. В Xamarin Studio откройте проект, измененный после изучения учебника [Приступая к работе с данными].

2. Нажмите кнопку **Запуск**, чтобы построить проект и запустить приложение, а затем введите текст длиннее 10 символов в текстовом поле и нажмите значок (**+**).

	Обратите внимание, что приложение вызывает необработанную ошибку в результате ответа 400 (Неверный запрос), возвращенного мобильной службой.

3. В файле TodoService.cs найдите текущую обработку исключения <code>try/catch</code> в методе **InsertTodoItemAsync** и замените <code>catch</code> на:

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

4. Найдите метод **OnAdd** в **TodoListViewController.cs**. Обновите метод, чтобы убедиться, что возвращаемый <code>index</code> не является <code>-1</code>, так как он возвращается при обработке исключения в **InsertTodoItemAsync**. В этом случае не нужно добавлять новую строку в <code>TableView</code>.

    if (index != -1) {
        TableView.InsertRows(new [] { NSIndexPath.FromItemSection(index, 0) },
            UITableViewRowAnimation.Top);
        itemText.Text = "";
    }


5. Повторно выполните сборку и запустите приложение.

	![][4]

	Обратите внимание, что ошибка будет обработана и для пользователя отобразится сообщение об ошибке.


## <a name="next-steps"> </a>Дальнейшие действия

Теперь, после прохождения этого учебника, рассмотрите возможность перехода к заключительному учебнику в этом ряду, касающемся данных: [Уточнение запросов c разбиением по страницам].

Серверные скрипты также используются при авторизации пользователей и для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

* [Авторизация пользователей с помощью скриптов]
  <br/>Сведения о фильтрации данных на основе идентификатора пользователя, прошедшего проверку.

* [Приступая к работе с push-уведомлениями]
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительная информация о регистрации и использовании серверных сценариев.

<!-- Anchors. -->
[Добавление проверки длины строки]: #string-length-validation
[Обновление клиента для поддержки проверки]: #update-client-validation
[Добавление метки времени при вставке]: #add-timestamp
[Обновление клиента для отображения метки времени]: #update-client-timestamp
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started-xamarin-ios
[Авторизация пользователей с помощью скриптов]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Уточнение запросов с разбиением по страницам]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Приступая к работе с данными]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Приступая к работе с аутентификацией]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Приступая к работе с push-уведомлениями]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Портал управления]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330


<!--HONumber=52-->