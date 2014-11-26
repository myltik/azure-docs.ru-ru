<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Приступая к работе с проверкой подлинности в мобильных службах

> [AZURE.SELECTOR-LIST (Платформа | Сервер )]
- [(iOS | .NET)](/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/)
- [(iOS | JavaScript)](/ru-ru/documentation/articles/mobile-services-ios-get-started-users/)
- [(Windows C# | .NET)](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)
- [(Windows C# | Javascript)](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users/)
- [(Windows JavaScript | .NET)](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/)
- [(Windows JavaScript | Javascript)](/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
- [(Windows Phone | .NET)](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/)
- [(Windows Phone | Javascript)](/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users/)
- [(Android | .NET)](/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/)
- [(Android | Javascript)](/ru-ru/documentation/articles/mobile-services-android-get-started-users/)
- [(Xamarin iOS | .NET)](/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/)
- [(Xamarin iOS | Javascript)](/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/)
- [(HTML | Javascript)](/ru-ru/documentation/articles/mobile-services-html-get-started-users/)
- [(Xamarin Android | .NET)](/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/)
- [(Xamarin Android | Javascript)](/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/)
- [(Appcelerator | Javascript)](/ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users/)

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб][Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности][Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению][Добавление проверки подлинности к приложению]
4.  [Хранение маркеров аутентификации в клиенте][Хранение маркеров аутентификации в клиенте]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами]

> [WACOM.NOTE]В этом учебнике показан поток аутентификации, которым управляют мобильные службы с помощью различных поставщиков удостоверений. Этот метод можно легко настроить, и он поддерживает нескольких поставщиков. Информацию о том, как вместо этого использовать Live Connect для аутентификации, управляемой клиентом, и предоставления единого входа в приложении для Windows Phone, см. в разделе [Единый вход для приложений Windows Phone с использованием Live Connect][Единый вход для приложений Windows Phone с использованием Live Connect]. В случае применения аутентификации, управляемой клиентом, ваше приложение получает доступ к дополнительным данным о пользователе, управляемым поставщиком удостоверений. Чтобы получить те же данные о пользователе в мобильной службе, вызовите в серверных сценариях функцию **user.getIdentities()**. Дополнительную информацию см. в [этой записи](http://go.microsoft.com/fwlink/p/?LinkId=506605).

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

1.  (Необязательно) Выполните шаги, указанные в пункте <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Регистрация пакета приложения магазина Windows для проверки подлинности Microsoft</a>.

    <div class="dev-callout"><b>Примечание.</b>
    <p>Этот шаг является необязательным, так как он применяется только к поставщику входа учетной записи Майкрософт. При регистрации сведений пакета приложений магазина Windows с помощью мобильных служб клиент сможет повторно использовать учетные данные для входа в учетную запись Майкрософт для осуществления единого входа. Если этого не сделать, пользователям входа учетной записи Майкрософт придется осуществлять вход в систему при каждом вызове метода входа в систему. Выполните этот шаг, если вы планируете использовать поставщик удостоверений учетной записи Майкрософт.</p>
    </div>

Мобильная служба и приложение теперь настроены для работы с выбранным поставщиком проверки подлинности.

## <a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

1.  Откройте проект, созданный после завершения обучения, в Visual Studio 2012 Express для Windows 8 <a href="/ru-ru/develop/mobile/tutorials/get-started/">Приступая к работе с мобильными службами</a>.

2.  Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).

    Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

## <a name="tokens"></a>Хранение маркеров авторизации в клиенте

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей мобильных служб на стороне службы], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.

 



  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
  [Добавление проверки подлинности к приложению]: #add-authentication
  [Хранение маркеров аутентификации в клиенте]: #tokens
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
  [Единый вход для приложений Windows Phone с использованием Live Connect]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
  [этой записи]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [Авторизация пользователей мобильных служб на стороне службы]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts
