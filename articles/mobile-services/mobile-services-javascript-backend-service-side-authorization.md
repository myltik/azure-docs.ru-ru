<properties
	pageTitle="Авторизация пользователей на стороне службы в мобильных службах с помощью серверной части JavaScript | Центр разработчиков для мобильных устройств"
	description="Информация об авторизации пользователей в серверной части JavaScript мобильных служб Azure"
	services="mobile-services"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.topic="article"
	ms.date="2/18/2015"
	ms.author="krisragh"/>

# Авторизация пользователей на стороне службы в мобильных службах

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

В этом разделе показано, как использовать сценарии на стороне сервера для авторизации пользователей. В этом учебнике описывается регистрация сценариев в службах мультимедиа Azure, фильтрация запросов на основе идентификаторов пользователей и предоставление пользователям доступа только к их данным.

Этот учебник создан на основе краткого руководства по мобильным службам и учебника [Добавление проверки подлинности в существующее приложение мобильных служб]. Сначала ознакомьтесь с учебником [Добавление проверки подлинности в существующее приложение мобильных служб].

## <a name="register-scripts"></a>Регистрация скриптов

1. Войдите на [портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свою мобильную службу. Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

2. Щелкните **Сценарий** и выберите операцию **Вставить**.

   	![][2]

3. Замените имеющийся сценарий следующей функцией и нажмите кнопку **Сохранить**: Этот сценарий добавляет идентификатор прошедшего аутентификацию пользователя в элемент перед его вставкой.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }


    > [AZURE.NOTE] [Dynamic schema must be enabled](https://msdn.microsoft.com/library/azure/jj193175.aspx), чтобы это сработало. Для новых мобильных служб этот параметр включен по умолчанию.

5. Аналогично, замените имеющуюся операцию **Read** на следующую функцию. Этот сценарий фильтрует возвращаемые объекты TodoItem так, чтобы пользователь получал только элементы, которые вставил он сам.

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }




## <a name="test-app"></a>Тестирование приложения

1. Обратите внимание, что теперь при запуске клиентского приложения элементы не возвращаются, несмотря на то, что они уже добавлены в таблицу _TodoItem_ из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца user ID и теперь имеют значения NULL. Убедитесь, что с добавляемыми элементами связано значение userId в таблице _TodoItem_.

2. При наличии дополнительных учетных записей убедитесь, что пользователи могут просмотреть только свои собственные данные. Для этого можно закрыть и удалить приложение, а затем запустить его снова. Открыв диалоговое окно ввода учетных данных для входа, задайте другое имя входа, а затем проверьте, не отображаются ли элементы, введенные во время предыдущего входа.

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-javascript-backend-service-side-authorization/mobile-insert-script-users.png
[3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->

[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-with-data-ios
[Добавление проверки подлинности в существующее приложение мобильных служб]: /develop/mobile/tutorials/get-started-with-users-ios
[Add Push Notifications to Existing App]: /develop/mobile/tutorials/get-started-with-push-ios

[портал управления Azure]: https://manage.windowsazure.com/

<!--HONumber=54--> 