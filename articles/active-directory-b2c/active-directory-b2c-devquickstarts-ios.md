---
title: "Azure Active Directory B2C. Использование AppAuth в приложении iOS"
description: "В этой статье описано, как создать приложение iOS, которое использует AppAuth для управления удостоверениями и аутентификации пользователей в Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: ios
author: PatAltimore
manager: mtillman
editor: parakhj
ms.custom: seo
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objectivec
ms.topic: article
ms.date: 03/07/2017
ms.author: saeeda
ms.openlocfilehash: b4f46129a7a18e4653d714599630d6cdddfff4ed
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C. Вход с помощью приложения iOS

Платформа Microsoft Identity использует открытые стандарты, такие как OAuth2 и OpenID Connect. При использовании протокола открытого стандарта доступен более широкий выбор библиотек для интеграции со службами. Это и другие подобные руководства предназначены помочь разработчикам в написании приложений, которые подключаются к платформе Microsoft Identity. Большинство библиотек, в которых реализована [спецификация RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749), могут подключаться к платформе Microsoft Identity.

> [!WARNING]
> Корпорация Майкрософт не предоставляет исправления для библиотек сторонних производителей и не выполняла их проверку. В этом примере используется библиотека стороннего производителя с именем AppAuth, которая была протестирована в основных сценариях на совместимость со службой Azure AD B2C. Проблемы и запросы возможностей следует отправлять в проекты с открытым кодом библиотеки. Дополнительные сведения см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Если вы еще не работали с OAuth2 или OpenID Connect, вы не поймете большую часть примера конфигурации. Для начала мы рекомендуем просмотреть [общие сведения о протоколе](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C
Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md), прежде чем продолжить.

## <a name="create-an-application"></a>Создание приложения
Затем необходимо создать приложение в каталоге B2C. При регистрации приложения в Azure AD поступят сведения, необходимые для безопасного взаимодействия с вашим приложением. Чтобы создать мобильное приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

* Включите в приложение **собственный клиент**.
* Скопируйте **идентификатор приложения** , назначенный приложению. Этот GUID понадобится позже.
* Задайте **URI перенаправления** с настраиваемой схемой (например, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Этот URI понадобится позже.

## <a name="create-your-policies"></a>Создание политик
В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Это приложение предусматривает одну процедуру идентификации, сочетающую в себе вход и регистрацию. Создайте эту политику, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). При создании политики обязательно сделайте следующее:

* В разделе **Sign-up attributes** (Атрибуты регистрации) выберите атрибут **Отображаемое имя**.  Можно также выбрать другие атрибуты.
* В разделе **Application claims** (Утверждения приложения) выберите утверждения **Отображаемое имя** и **ИД объекта пользователя**. Можно также выбрать другие утверждения.
* Скопируйте **имя** каждой политики после ее создания. При сохранении политики к ее имени добавляется префикс `b2c_1_`.  Эти имена политик понадобятся вам позже.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

После создания политик можно приступать к сборке приложения.

## <a name="download-the-sample-code"></a>Скачивание примера кода
Мы разместили рабочий пример, использующий AppAuth с Azure AD B2C, [на сайте GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Вы можете скачать код и запустить его. Чтобы использовать собственный клиент Azure AD B2C, следуйте инструкциям в файле [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Этот пример был создан по инструкциям в файле README [проекта AppAuth для iOS на сайте GitHub](https://github.com/openid/AppAuth-iOS). Чтобы получить дополнительные сведения о работе этого примера и библиотеки, обратитесь к файлу README для AppAuth на сайте GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Изменение приложения для использования Azure AD B2C с AppAuth

> [!NOTE]
> AppAuth поддерживает iOS 7 и более поздние версии.  Однако для поддержки социальных имен входа в Google необходим SFSafariViewController, который поддерживает iOS 9 и более поздние версии.
>

### <a name="configuration"></a>Параметр Configuration

Вы можете настроить взаимодействие с Azure AD B2C, указав URI конечной точки авторизации и конечной точки токена.  Для создания URI вам потребуются следующие сведения:
* идентификатор клиента (например, contoso.onmicrosoft.com);
* имя политики (например, B2C\_1\_SignUpIn).

URI конечной точки токена можно создать, заменив Tenant\_ID (идентификатор клиента) и Policy\_Name (имя политики) в следующем URL-адресе:

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

URI конечной точки авторизации можно создать, заменив Tenant\_ID (идентификатор клиента) и Policy\_Name (имя политики) в следующем URL-адресе:

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Выполните следующий код для создания объекта AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Авторизация

После настройки или извлечения конфигурации службы авторизации можно сформировать запрос авторизации. Для создания запроса вам потребуются следующие сведения:  
* идентификатор клиента (например, 00000000-0000-0000-0000-000000000000);
* URI перенаправления с настраиваемой схемой (например, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect).

Оба элемента нужно сохранить при [регистрации приложения](#create-an-application).

```objc
OIDAuthorizationRequest *request = 
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow = 
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Чтобы настроить приложение для обработки перенаправления к URI с настраиваемой схемой, необходимо обновить список схем URL-адреса в файле Info.pList:
* Откройте файл Info.pList.
* Наведите указатель мыши на строку, например "Код типа ОС для пакета", и щелкните символ \+.
* Переименуйте новую строку на "Типы URL-адреса".
* Щелкните стрелку слева от элемента "Типы URL-адреса", чтобы открыть дерево.
* Щелкните стрелку слева от элемента "Элемент 0", чтобы открыть дерево.
* Переименуйте первый элемент под элементом 0 в "Схемы URL-адресов".
* Щелкните стрелку слева от элемента "Схемы URL-адресов", чтобы открыть дерево.
* В столбце "Значение" слева от элемента "Элемент 0", под элементом "Схемы URL-адресов", находится пустое поле.  Присвойте ему значение уникальной схемы своего приложения.  Оно должно соответствовать схеме, указанной в redirectURL при создании объекта OIDAuthorizationRequest.  В этом примере используется схема com.onmicrosoft.fabrikamb2c.exampleapp.

Сведения о завершении остальной части процесса см. в [руководстве по AppAuth](https://openid.github.io/AppAuth-iOS/). Если вам нужно быстро приступить к работе с приложением, ознакомьтесь с [этим примером](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Следуйте указаниям в файле [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md), чтобы ввести собственные значения для настройки Azure AD B2C.

Мы всегда рады вашим отзывам и предложениям! Если у вас возникли трудности с выполнением действий, описанных в этой статье или у вас есть рекомендации по улучшению этого материала, поделитесь с нами своими наблюдениями, воспользовавшись формой внизу страницы. Запросы функций оставляйте на форуме [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
