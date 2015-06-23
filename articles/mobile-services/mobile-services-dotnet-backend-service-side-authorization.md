<properties
	pageTitle="Авторизация пользователей на стороне службы в мобильных службах с помощью серверной части .NET | Центр разработчиков для мобильных устройств"
	description="Информация о том, как осуществить авторизацию пользователей в серверной части .NET мобильных служб Azure."
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

В этом разделе показано, как использовать логику на стороне сервера для авторизации пользователей. В этом учебнике описывается изменение методов доступа к данным в .NET, фильтрация запросов на основе идентификаторов пользователей и предоставление пользователям доступа только к их данным.

Этот учебник создан на основе краткого руководства по мобильным службам и учебника [Добавление проверки подлинности в существующее приложение мобильных служб]. Сначала ознакомьтесь с учебником [Добавление проверки подлинности в существующее приложение мобильных служб].

## <a name="register-scripts"></a>Изменение методов доступа к данным

1. В Visual Studio откройте проект для мобильного устройства, разверните папку DataObjects и откройте файл **TodoItem.cs**. Класс **TodoItem** определяет объект данных, и вам необходимо добавить свойство **UserId** для использования фильтрации. Добавьте в класс **TodoItem** следующее новое свойство UserId:

		public string UserId { get; set; }

	>[AZURE.NOTE]Чтобы внести это изменение модели данных и сохранить существующие данные в базе, необходимо использовать [Code First Migrations](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

2. В Visual Studio разверните папку "Контроллеры", а затем откройте файл **TodoItemController.cs**. Найдите метод **PostTodoItem** и добавьте следующий код в начало метода: Этот код добавляет в элемент идентификатор пользователя, прошедшего аутентификацию, прежде чем вставить этот элемент в таблицу TodoItem.

			// Get the logged in user
			var currentUser = User as ServiceUser;

			// Set the user ID on the item
			item.UserId = currentUser.Id;

3. Найдите метод **GetAllTodoItems** и замените имеющуюся инструкцию **return** на следующую строку кода: Этот запрос фильтрует возвращаемые объекты TodoItem таким образом, чтобы каждый пользователь получил только элементы, которые он вставил.

				// Get the logged in user
				var currentUser = User as ServiceUser;

				return Query().Where(todo => todo.UserId == currentUser.Id);

4. повторно опубликовать проект мобильной службы в Azure.


## <a name="test-app"></a>Тестирование приложения

1. Обратите внимание, что теперь при запуске клиентского приложения элементы не возвращаются, несмотря на то, что они уже были добавлены в базу данных из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца user ID и теперь имеют значения NULL.

2. При наличии дополнительных учетных записей убедитесь, что пользователи могут просмотреть только свои собственные данные. Для этого можно закрыть и удалить приложение, а затем запустить его снова. Открыв диалоговое окно ввода учетных данных для входа, задайте другое имя входа, а затем проверьте, не отображаются ли элементы, введенные во время предыдущего входа.



<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with data]: mobile-services-dotnet-backend-ios-get-started-data.md
[Добавление проверки подлинности в существующее приложение мобильных служб]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md

[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!--HONumber=54--> 