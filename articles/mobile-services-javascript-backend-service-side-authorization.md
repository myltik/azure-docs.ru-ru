<properties
	pageTitle="Авторизация пользователей на стороне службы в мобильных службах с помощью серверной части JavaScript | Центр разработчиков для мобильных устройств"
	description="Информация об авторизации пользователей в серверной части JavaScript мобильных служб Azure."
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

> [AZURE.SELECTOR-LIST (Платформа | Серверная часть)]
- [(Любая | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Любая | JavaScript)](mobile-services-javascript-backend-service-side-authorization.md)

В этом разделе показано, как использовать сценарии на стороне сервера для авторизации пользователей. В этом учебнике описывается регистрация сценариев в службах мультимедиа Azure, фильтрация запросов на основе идентификаторов пользователей и предоставление пользователям доступа только к их данным.

Этот учебник создан на основе краткого руководства по мобильным службам и учебника [Добавление аутентификации в существующее приложение мобильных служб]. Сначала ознакомьтесь с учебником [Добавление аутентификации в существующее приложение мобильных служб].

## <a name="register-scripts"></a>Регистрация сценариев

1. Войдите на [портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свою мобильную службу. Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

2. Выберите элемент **Скрипт**, а затем - операцию **Вставить**.

   	![][2]

3. Замените существующий скрипт указанной ниже функцией и нажмите кнопку **Сохранить**. Этот сценарий добавляет идентификатор прошедшего аутентификацию пользователя в элемент перед его вставкой.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }


    > [AZURE.NOTE] Для правильной работы [должна быть включена динамическая схема](https://msdn.microsoft.com/library/azure/jj193175.aspx). Для новых мобильных служб этот параметр включен по умолчанию.

5. Аналогично, замените имеющуюся операцию **Read** на следующую функцию. Этот сценарий фильтрует возвращаемые объекты TodoItem так, чтобы пользователь получал только элементы, которые вставил он сам.

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }




## <a name="test-app"></a>Тестирование приложения

1. Обратите внимание, что теперь при запуске клиентского приложения элементы не возвращаются, несмотря на то, что они уже были добавлены в таблицу _TodoItem_ из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца user ID и теперь имеют значения NULL. Убедитесь, что с добавляемыми элементами связано значение userId в таблице _TodoItem_.

2. При наличии дополнительных учетных записей убедитесь, что пользователи могут просмотреть только свои собственные данные. Для этого можно закрыть и удалить приложение, а затем запустить его снова. Открыв диалоговое окно ввода учетных данных для входа, задайте другое имя входа, а затем проверьте, не отображаются ли элементы, введенные во время предыдущего входа.

<!-- Anchors. -->
[Регистрация серверных сценариев]: #register-scripts
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->

[Push-уведомления Windows и Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Добавление мобильных служб в существующее приложение]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
[Добавление аутентификации в существующее приложение мобильных служб]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[Добавление Push-уведомлений в существующее приложение]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios

[портал управления Azure]: https://manage.windowsazure.com/

<!--HONumber=45--> 
