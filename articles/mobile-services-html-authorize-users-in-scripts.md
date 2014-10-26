<properties pageTitle="Service-side authorization (HTML) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Авторизация пользователей мобильных служб на стороне службы

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash;JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android">Android</a><a href="/ru-ru/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML" class="current">HTML</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-html-authorize-users-in-scripts/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a></div>

В этом разделе показано, как использовать серверные скрипты для авторизации пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения HTML. В этом учебнике вы зарегистрируете скрипты с помощью мобильных служб для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

## <a name="register-scripts"></a>Регистрация сценариев

Поскольку данное приложение быстрого запуска считывает и вставляет данные, необходимо зарегистрировать скрипты для этих операций в таблице TodoItem.

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

    ![][]

2.  Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

    ![][1]

3.  Щелкните **Сценарий** и выберите операцию **Вставить**.

    ![][2]

4.  Замените имеющийся сценарий следующей функцией и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Этот скрипт добавляет в элемент значение userId, которое является идентификатором пользователя, прошедшего проверку подлинности, до его вставки в таблицу TodoItem.

    <div class="dev-callout"><b>Примечание.</b>
<p>При первом выполнении этого сценария вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>userId</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения ее следует отключить.</p>
</div>

5.  Повторите шаги 3 и 4, чтобы заменить существующую операцию **Read** следующей функцией:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Этот скрипт фильтрует возвращаемые объекты TodoItem, чтобы каждый пользователь получил только элементы, которые он вставил.

## Тестирование приложения

1.  В браузере перейдите на страницу index.html приложения, а затем войдите с помощью выбранного поставщика удостоверений.

    Обратите внимание, что в этом случае не возвращается никаких элементов, хотя в таблице TodoItem уже есть элементы из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца userId и теперь имеют значения NULL.

2.  В приложении введите текст в поле **Введите новую задачу** и нажмите кнопку **Добавить**.

    ![][3]

    При этом в таблицу TodoItem мобильной службы вставляется текст и идентификатор пользователя. Так как новый элемент имеет правильное значение userId, он возвращается мобильной службой и отображается во втором столбце.

3.  В таблице **todoitem** на [портале управления][портал управления Azure] нажмите кнопку **Обзор** и убедитесь, что каждый новый добавляемый элемент теперь имеет соответствующее значение userId.

4.  (Необязательно.) При наличии дополнительных учетных записей вы можете проверить, что пользователи могут просмотреть только свои собственные данные. Для этого можно закрыть приложение (Alt+F4) и затем запустить его снова. Когда появится диалоговое окно учетных данных входа, введите другое имя входа и убедитесь, что элементы, введенные с использованием предыдущей учетной записи, не отображаются.

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с проверкой подлинности. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с данными][Приступая к работе с данными]

    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]

    Узнайте больше о регистрации и использовании серверных сценариев.

-   [Справочник принципов использования мобильных служб HTML/JavaScript][Справочник принципов использования мобильных служб HTML/JavaScript]

    Узнайте больше о том, как использовать мобильные службы с HTML и JavaScript.



  [Магазин Windows — C#]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "Магазин Windows — C#"
  [Магазин Windows —JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "Магазин Windows — JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android"
  [HTML]: /ru-ru/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML"
  [Xamarin.iOS]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android"
  [Сервер JavaScript]: /ru-ru/documentation/articles/mobile-services-html-authorize-users-in-scripts/ "Сервер JavaScript"
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-html
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-html-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-html-authorize-users-in-scripts/mobile-quickstart-startup-html.png
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-html
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=262293
  [Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client
