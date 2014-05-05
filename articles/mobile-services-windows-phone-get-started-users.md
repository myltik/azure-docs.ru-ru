<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Приступая к работе с аутентификацией" pageTitle="Приступая к работе с аутентификацией (Windows Phone) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Использование мобильных служб для проверки подлинности пользователей приложения Windows Phone с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с аутентификацией в мобильных службах" authors="glenga" solutions="" manager="" editor="" />

# Приступая к работе с аутентификацией в мобильных службах
<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/ru-ru/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, для этого используется поставщик удостоверений, который поддерживается мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">10:50</span></div>
</div>  

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для проверки подлинности и конфигурация мобильных служб</h2>

Чтобы иметь возможность проверять подлинность пользователей, необходимо зарегистрировать приложение у поставщика удостоверений. Затем необходимо зарегистрировать секрет клиента, созданный поставщиком, в мобильных службах.

1. Выполните вход на [портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

   	![][1]

2. Щелкните вкладку **Панель мониторинга** и запишите значение **URL-адрес мобильной службы**.

   	![][2]

    От вас может потребоваться предоставить это значение поставщику удостоверений при регистрации приложения.

3. Выберите поддерживаемый поставщик удостоверений из приведенного ниже списка и выполните действия для регистрации приложения у этого поставщика:

 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Учетная запись Майкрософт</a>
 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Вход в Facebook</a>
 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Вход в Twitter</a>
 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Вход в Google</a>
 - <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Не забудьте записать удостоверение клиента и значения секрета, созданные поставщиком.

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет, созданный поставщиком, входит в важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе с вашим приложением.</p>
    </div>

4. На портале управления откройте вкладку **Удостоверение**, введите идентификатор приложения и значения общего секрета, полученные у поставщика удостоверений, а затем щелкните **Сохранить**.

   	![][3]

Мобильная служба и приложение теперь настроены для работы с выбранным поставщиком проверки подлинности.

##<a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

1. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![][4]

2. Откройте вкладку **Разрешения**, установите все разрешения в состояние **Только прошедшие проверку пользователи** и нажмите кнопку **Сохранить**. Это обеспечит, что только прошедший проверку пользователь сможет производить все операции с таблицей **TodoItem**. Это также упрощает скрипты в следующем учебнике, так как в них не нужно учитывать возможность существования анонимных пользователей.

   	![][5]

3. Откройте проект, созданный при прохождении учебника [Приступая к работе с мобильными службами], в Visual Studio 2012 Express для Windows Phone. 

4. Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный). 
   
   	Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить проверку подлинности пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Authorize users with scripts], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. 

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-wp8
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/started-with-data-wp8
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Портал управления Azure]: https://manage.windowsazure.com/

