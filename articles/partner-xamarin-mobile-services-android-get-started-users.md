<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-android" urlDisplayName="Get Started with Authentication (Xamarin.Android)" pageTitle="Get started with authentication (Xamarin.Android) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.Android" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with authentication in Mobile Services" services="mobile-services" documentationCenter="Mobile" manager="dwrede" authors="donnam"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Приступая к работе с проверкой подлинности в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как аутентифицировать пользователей в мобильных службах Azure с помощью приложения Xamarin.Android. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб][Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности][Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению][Добавление проверки подлинности к приложению]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

Для изучения этого учебника необходимы Xamarin.Android и пакет SDK для Android версии 4.2 или более поздней.

## <a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для аутентификации и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a><span class="short-header">Ограничение разрешений</span>Ограничение разрешений для аутентифицированных пользователей

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  В Eclipse откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

2.  В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (Не санкционировано).

    Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

## <a name="add-authentication"></a><span class="short-header">Добавление аутентификации</span>Добавление аутентификации в приложение

1.  Добавьте в класс **TodoActivity** следующее свойство:

            private MobileServiceUser user;

2.  Добавьте в класс **TodoActivity** следующий метод:

            private async Task Authenticate()
            {
                try
                {
                    user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
            }

    При этом создается новый метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с помощью имени входа в учетную запись Майкрософт. Открывается диалоговое окно, в котором отображается идентификатор пользователя, прошедшего проверку подлинности. Без успешной проверки подлинности продолжение невозможно.

    <div class="dev-callout"><b>Примечание.</b>
<p>Если используется поставщик удостоверений, отличный от поставщика Майкрософт, измените значение, передаваемое в метод <strong>login</strong> (см. выше), на одно из следующих: <i>Facebook</i>, <i>Google</i>, <i>Twitter</i> или <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Добавьте в метод **OnCreate** следующую строку после кода, который формирует экземпляр объекта `MobileServiceClient`.

        await Authenticate();

    Этот вызов запускает процесс проверки подлинности и асинхронно ждет его.

4.  Переместите оставшийся код после `await Authenticate();` в методе **OnCreate** в новый метод **CreateTable**, который выглядит следующим образом:

            private async Task CreateTable()
            {
                // Get the Mobile Service Table instance to use
                todoTable = client.GetTable<TodoItem>();
                textNewTodo = FindViewById<EditText>(Resource.Id.textNewTodo);
                // Create an adapter to bind the items with the view
                adapter = new TodoItemAdapter(this, Resource.Layout.Row_List_To_Do);
                var listViewTodo = FindViewById<ListView>(Resource.Id.listViewTodo);
                listViewTodo.Adapter = adapter;
                // Load the items from the Mobile Service
                await RefreshItemsFromTableAsync();
            }

5.  Затем вызовите новый метод **CreateTable** в **OnCreate** после вызова метода **Authenticate**, добавленного на шаге 2:

        await CreateTable();

6.  В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений.

    После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## Получение полного примера

Загрузите [полный пример проекта][полный пример проекта]. Не забудьте обновить переменные **applicationURL** и **applicationKey** с использованием своих параметров Azure.

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов][Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
  [Добавление проверки подлинности к приложению]: #add-authentication
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-android
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
