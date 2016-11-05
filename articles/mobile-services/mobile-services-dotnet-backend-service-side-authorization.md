---
title: Авторизация пользователей на стороне службы в мобильной службе .NET | Microsoft Docs
description: Узнайте, как ограничить доступ для авторизации пользователей в серверной мобильной службе .NET
services: mobile-services
documentationcenter: windows
author: krisragh
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.topic: article
ms.devlang: dotnet
ms.date: 07/21/2016
ms.author: krisragh

---
# Авторизация пользователей на стороне службы в мобильных службах
> [!div class="op_single_selector"]
> * [Сервер .NET.](mobile-services-dotnet-backend-service-side-authorization.md)
> * [Серверная служба JavaScript](mobile-services-javascript-backend-service-side-authorization.md)
> 
> 

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Аналогичные сведения для мобильных приложений см. в статье [How to: Restrict data access for authorized users](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#authorize), ссылка на которую приведена в статье "Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure".
> 
> 

В этом разделе показано, как использовать логику на стороне сервера для авторизации пользователей. В этом учебнике описывается изменение контролеров таблицы, фильтрация запросов на основе идентификаторов пользователей и предоставление пользователям доступа только к их данным. Фильтрация результатов запроса по идентификатору пользователя — это самая простая форма авторизации. В зависимости от конкретного сценария вам также может потребоваться создать таблицы пользователей или ролей для отслеживания более подробных сведений авторизации пользователей, например конечных точек, к которым данному пользователю разрешен доступ.

Этот учебник создан на основе краткого руководства по мобильным службам и учебника [Добавление проверки подлинности в существующее приложение мобильных служб]. Сначала ознакомьтесь с учебником [Добавление проверки подлинности в существующее приложение мобильных служб].

## <a name="register-scripts"></a>Изменение методов доступа к данным
1. В Visual Studio откройте проект для мобильного устройства, разверните папку DataObjects и откройте файл **TodoItem.cs**. Класс **TodoItem** определяет объект данных, и вам необходимо добавить свойство **UserId** для использования фильтрации. Добавьте в класс **TodoItem** следующее новое свойство UserId:
   
        public string UserId { get; set; }
   
   > [!NOTE]
   > Чтобы внести это изменение модели данных и сохранить существующие данные в базе, необходимо использовать [Code First Migrations](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).
   > 
   > 
2. В Visual Studio разверните папку "Контроллеры", откройте файл **TodoItemController.cs** и добавьте следующее выражение:
   
        using Microsoft.WindowsAzure.Mobile.Service.Security;
3. Найдите метод **PostTodoItem** и добавьте следующий код в начало метода:
   
        // Get the logged in user
        var currentUser = User as ServiceUser;
   
        // Set the user ID on the item
        item.UserId = currentUser.Id;
   
    Этот код добавляет в элемент идентификатор пользователя, прошедшего аутентификацию, прежде чем вставить этот элемент в таблицу TodoItem.
4. Найдите метод **GetAllTodoItems** и замените имеющуюся инструкцию **return** на следующую строку кода:
   
        // Get the logged in user
        var currentUser = User as ServiceUser;
   
        return Query().Where(todo => todo.UserId == currentUser.Id);
   
    Этот запрос фильтрует возвращаемые объекты TodoItem таким образом, чтобы каждый пользователь получил только элементы, которые он вставил.
5. повторно опубликовать проект мобильной службы в Azure.

## <a name="test-app"></a>Тестирование приложения
1. Обратите внимание, что теперь при запуске клиентского приложения элементы не возвращаются, несмотря на то, что они уже были добавлены в базу данных из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца user ID и теперь имеют значения NULL.
2. При наличии дополнительных учетных записей убедитесь, что пользователи могут просмотреть только свои собственные данные. Для этого можно закрыть и удалить приложение, а затем запустить его снова. Открыв диалоговое окно ввода учетных данных для входа, задайте другое имя входа, а затем проверьте, не отображаются ли элементы, введенные во время предыдущего входа.

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Добавление проверки подлинности в существующее приложение мобильных служб]: mobile-services-dotnet-backend-ios-get-started-users.md

<!---HONumber=AcomDC_0727_2016-->