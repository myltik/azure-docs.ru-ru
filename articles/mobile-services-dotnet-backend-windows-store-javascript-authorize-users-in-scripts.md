<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Авторизация пользователей мобильных служб на стороне службы

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="Магазин Windows &mdash; JavaScript" class="current">Магазин Windows &mdash; JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS">iOS</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/" title="Серверная часть .NET" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/"  title="Серверная часть JavaScript">Серверная часть JavaScript</a></div>

В этом разделе показано, как осуществить авторизацию пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения Магазина Windows. В этом учебнике вы добавите код в методы доступа к данным контроллера, которые используются для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

## <a name="register-scripts"></a>Изменение методов доступа к данным

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend][mobile-services-filter-user-results-dotnet-backend]]

## Тестирование приложения

1.  В Visual Studio откройте проект, измененный после завершения учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

2.  Нажмите клавишу F5, чтобы запустить приложение, а затем выполните вход с использованием выбранного поставщика удостоверений.

    Обратите внимание, что в этом случае не возвращается никаких элементов, хотя в таблице TodoItem уже есть элементы из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца userId и теперь имеют значения NULL.

3.  В приложении введите текст в поле **Insert a TodoItem** (Вставка TodoItem), а затем нажмите кнопку **Сохранить**.

    ![][]

    При этом в таблицу TodoItem мобильной службы вставляется текст и идентификатор пользователя. Так как новый элемент имеет правильное значение userId, он возвращается мобильной службой и отображается во втором столбце.

4.  (Необязательно) При наличии дополнительных учетных записей вы можете проверить, что пользователи могут просмотреть только свои собственные данные. Для этого можно закрыть приложение (Alt+F4) и затем запустить его снова. Когда появится диалоговое окно учетных данных входа, введите другое имя входа и убедитесь, что элементы, введенные с использованием предыдущей учетной записи, не отображаются.

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с проверкой подлинности. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с данными][Приступая к работе с данными]

    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

-   [Использование клиента HTML/JavaScript для мобильных служб Azure][Использование клиента HTML/JavaScript для мобильных служб Azure]

    Узнайте о том, как использовать мобильные службы с JavaScript и HTML.



  [Магазин Windows — C#]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "Магазин Windows — C#"
  [Магазин Windows — JavaScript]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "Магазин Windows — JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS"
  [Серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/ "Серверная часть .NET"
  [Серверная часть JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/ "Серверная часть JavaScript"
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [mobile-services-filter-user-results-dotnet-backend]: ../includes/mobile-services-filter-user-results-dotnet-backend.md
  []: ./media/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Использование клиента HTML/JavaScript для мобильных служб Azure]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library
