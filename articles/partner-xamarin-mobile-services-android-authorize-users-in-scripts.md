<properties linkid="develop-mobile-tutorials-authorize-users-in-scripts-xamarin-android" urlDisplayName="Авторизация пользователей с помощью скриптов (Xamarin.Android)" pageTitle="Авторизация пользователей с помощью скриптов (Xamarin.Android) — мобильные службы Azure" metaKeywords="авторизация пользователей Azure, авторизация скриптов Xamarin.Android, авторизация мобильных служб" description="Узнайте, как выполнить авторизацию пользователей с помощью скриптов в приложении мобильных служб Azure для Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Авторизация пользователей в мобильных службах с помощью скриптов" authors="" />

# Авторизация пользователей в мобильных службах с помощью скриптов
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet" title="Версия C# Магазина Windows">Версия C# Магазина Windows</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-js" title="Версия JavaScript Магазина Windows">Версия JavaScript Магазина Windows</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios" title="Xamarin.iOS">iOS C#</a><a href="/ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android" title="Xamarin.Android" class="current">Android C#</a>
</div>	

В этом разделе показано, как использовать серверные скрипты для авторизации пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения Xamarin.Android.  В этом учебнике вы зарегистрируете скрипты с помощью мобильных служб для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с аутентификацией]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с аутентификацией].  

## <a name="register-scripts"></a>Регистрация серверных скриптов
Поскольку данное приложение быстрого запуска считывает и вставляет данные, необходимо зарегистрировать скрипты для этих операций в таблице TodoItem.

1. Выполните вход на [портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свое приложение. 

   	![][0]

2. Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

   	![][1]

3. Щелкните элемент **Скрипт** и выберите операцию **Вставить**.

   	![][2]

4. Замените имеющийся скрип следующей функцией и нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Этот скрипт добавляет в элемент значение userId, которое является идентификатором пользователя, прошедшего проверку подлинности, до его вставки в таблицу TodoItem. 

    <div class="dev-callout"><b>Примечание.</b>
	<p>При первом выполнении этого скрипта вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>userId</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения в Магазине Windows ее следует отключить.</p>
    </div>


5. Повторите шаги 3 и 4, чтобы заменить существующую операцию **Read** следующей функцией:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Этот скрипт фильтрует возвращаемые объекты TodoItem, чтобы каждый пользователь получил только элементы, которые он вставил.

## Тестирование приложения

1. Откройте в Xamarin Studio или Visual Studio проект, измененный при завершении учебника [Приступая к работе с аутентификацией].

2. Щелкните **Запуск**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений. 

   	Обратите внимание, что в этом случае не возвращается никаких элементов, хотя в таблице TodoItem уже есть элементы из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца userId и теперь имеют значения NULL.

3. В приложении введите текст в поле **Вставить TodoItem**, а затем нажмите кнопку **Сохранить**.

   	При этом в таблицу TodoItem мобильной службы вставляется текст и идентификатор пользователя. Так как новый элемент имеет правильное значение userId, он возвращается мобильной службой и отображается во втором столбце.

5. В таблице **todoitem** в [портале управления][Azure Management Portal] нажмите кнопку **Обзор** и убедитесь, что каждый новый добавляемый элемент теперь имеет соответствующее значение userId.

6. (Необязательно) При наличии дополнительных учетных записей можно проверить, что пользователи могут просмотреть только свои собственные данные, закрыв приложение и затем запустив его снова. Когда появится диалоговое окно учетных данных входа, введите другое имя входа и убедитесь, что элементы, введенные с использованием предыдущей учетной записи, не отображаются.

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с проверкой подлинности. Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о регистрации и использовании серверных скриптов.

<!-- Anchors. -->
[Регистрация серверных скриптов]: #register-scripts
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-android
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Портал управления Azure]: https://manage.windowsazure.com/

