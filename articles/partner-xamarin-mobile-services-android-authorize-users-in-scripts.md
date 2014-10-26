<properties linkid="develop-mobile-tutorials-authorize-users-in-scripts-xamarin-android" urlDisplayName="Authorize Users in Scripts (Xamarin.Android)" pageTitle="Authorize users in scripts (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure authorizing user, Xamarin.Android scripts authorization, authorize mobile services" description="Learn how to authorize users with scripts in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Use scripts to authorize users in Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Авторизация пользователей в мобильных службах с помощью сценариев

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet" title="Версия C# Магазина Windows">Версия C# Магазина Windows</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-js" title="Версия JavaScript Магазина Windows">Версия JavaScript Магазина Windows</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios" title="Xamarin.iOS">iOS C#</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android" title="Xamarin.Android" class="current">Android C#</a>
</div>

В этом разделе показано, как использовать серверные скрипты для авторизации пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения Xamarin.Android. В этом учебнике вы зарегистрируете скрипты с помощью мобильных служб для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.

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
<p>При первом выполнении этого сценария вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>userId</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения в Магазине Windows ее следует отключить.</p>
</div>

5.  Повторите шаги 3 и 4, чтобы заменить существующую операцию **Read** следующей функцией:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Этот скрипт фильтрует возвращаемые объекты TodoItem, чтобы каждый пользователь получил только элементы, которые он вставил.

## Тестирование приложения

1.  Откройте в Xamarin Studio или Visual Studio проект, измененный после изучения учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

2.  Запустите приложение кнопкой **Запуск** и выполните вход с помощью выбранного поставщика удостоверений.

    Обратите внимание, что в этом случае не возвращается никаких элементов, хотя в таблице TodoItem уже есть элементы из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца userId и теперь имеют значения NULL.

3.  В приложении введите текст в поле **Insert a TodoItem** (Вставка TodoItem), а затем нажмите кнопку **Сохранить**.

    При этом в таблицу TodoItem мобильной службы вставляется текст и идентификатор пользователя. Так как новый элемент имеет правильное значение userId, он возвращается мобильной службой и отображается во втором столбце.

4.  В таблице **todoitem** на [портале управления][портал управления Azure] щелкните **Обзор** и убедитесь, что каждый новый добавляемый элемент теперь имеет соответствующее значение userId.

5.  (Необязательно.) При наличии дополнительных учетных записей, вы можете проверить, что пользователи могут просмотреть только свои собственные данные. Для этого можно закрыть приложение и затем запустить его снова. Когда появится диалоговое окно учетных данных входа, введите другое имя входа и убедитесь, что элементы, введенные с использованием предыдущей учетной записи, не отображаются.

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с проверкой подлинности. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с данными][Приступая к работе с данными]
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
    Узнайте больше о регистрации и использовании серверных сценариев.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Версия C# Магазина Windows]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet "Версия C# Магазина Windows"
  [Версия JavaScript Магазина Windows]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-js "Версия JavaScript Магазина Windows"
  [Windows Phone]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-wp8 "Windows Phone"
  [iOS]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-ios "iOS"
  [Android]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-android "Android"
  [HTML]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-html "HTML"
  [iOS C#]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios "Xamarin.iOS"
  [Android C#]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android "Xamarin.Android"
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=262293
