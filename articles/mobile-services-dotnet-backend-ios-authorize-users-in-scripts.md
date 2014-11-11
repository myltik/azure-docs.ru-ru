<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Авторизация пользователей мобильных служб на стороне службы

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash; JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS" class="current">iOS</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/" title="Серверная часть .NET" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-ios-authorize-users-in-scripts/"  title="Серверная часть JavaScript">Серверная часть JavaScript</a></div>

В этом разделе показано, как проводить авторизацию пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения iOS. В этом учебнике вы добавите код в методы доступа к данным контроллера, которые используются для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

## <a name="register-scripts"></a>Изменение методов доступа к данным

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

## Тестирование приложения

1.  В Xcode откройте проект, измененный после завершения учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

2.  Чтобы выполнить сборку проекта, нажмите кнопку **Запуск**, запустите приложение в эмуляторе iPhone, а затем войдите с использованием выбранного поставщика удостоверений.

    Обратите внимание, что в этом случае не возвращается никаких элементов, хотя в таблице TodoItem уже есть элементы из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца userId и теперь имеют значения NULL.

3.  В приложении введите текст в поле **Insert a TodoItem** (Вставка TodoItem), а затем нажмите кнопку **Сохранить**.

    ![][0]

    При этом в таблицу TodoItem мобильной службы вставляется текст и идентификатор пользователя. Так как новый элемент имеет правильное значение userId, он возвращается мобильной службой и отображается во втором столбце.

4.  В таблице **todoitem** на [портале управления][портале управления Azure] нажмите кнопку **Обзор** и убедитесь, что каждый новый добавляемый элемент теперь имеет соответствующее значение userId.

5.  (Необязательно.) При наличии дополнительных учетных записей, вы можете проверить, что пользователи могут просмотреть только свои собственные данные. Для этого можно закрыть приложение и затем запустить его снова. Когда появится диалоговое окно учетных данных входа, введите другое имя входа и убедитесь, что элементы, введенные с использованием предыдущей учетной записи, не отображаются.

<!--## Next steps  This concludes the tutorials that demonstrate the basics of working with authentication. Consider finding out more about the following Mobile Services topics:  * [Get started with data]   <br/>Learn more about storing and querying data using Mobile Services.  * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app.    * [Mobile Services .NET How-to Conceptual Reference]   <br/>Learn more about how to use Mobile Services with .NET. --> 

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [mobile-services-filter-user-results-dotnet-backend]: ../includes/mobile-services-filter-user-results-dotnet-backend.md
  [0]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png
