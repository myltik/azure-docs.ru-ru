---
title: "Azure Active Directory B2C: получение токена с помощью приложения Android | Документация Майкрософт"
description: "В этой статье описывается, как создать приложение Android, которое использует AppAuth с Azure Active Directory B2C для управления удостоверениями пользователей и проверки подлинности пользователей."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/06/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: fdf9f494f30f11a00831b6c56a3d6ac40582c0ad
ms.lasthandoff: 03/14/2017


---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Azure AD B2C. Вход с помощью приложения Android

Платформа Microsoft Identity использует открытые стандарты, такие как OAuth2 и OpenID Connect. Это позволяет разработчикам использовать для интеграции со службами любые библиотеки. Чтобы объяснить разработчикам, как настроить сторонние библиотеки для подключения к платформе Microsoft Identity, мы написали несколько подобных этому пошаговых руководств. Большинство библиотек, в которых реализована [спецификация RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) , смогут подключаться к платформе Microsoft Identity.

> [!WARNING]
> Корпорация Майкрософт не предоставляет исправления для библиотек сторонних производителей и не выполняла их проверку. В этом примере используется библиотека стороннего производителя с именем AppAuth, которая была протестирована в основных сценариях на совместимость со службой Azure AD B2C. Проблемы и запросы возможностей следует отправлять в проекты с открытым кодом библиотеки. Дополнительные сведения см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).  
>
>

Если вы еще не работали с OAuth2 или OpenID Connect, вы не поймете большую часть примера конфигурации. Для начала мы рекомендуем просмотреть [общие сведения о протоколе](active-directory-b2c-reference-protocols.md).

Не все сценарии и компоненты Azure Active Directory поддерживаются платформой B2C.  Чтобы определить, следует ли вам использовать платформу B2C, ознакомьтесь с [ограничениями этой платформы](active-directory-b2c-limitations.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для данных всех ваших пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md), прежде чем продолжить.

## <a name="create-an-application"></a>Создание приложения

Затем необходимо создать приложение в каталоге B2C. Таким образом в Azure AD поступят сведения, необходимые для безопасного взаимодействия с вашим приложением. Чтобы создать мобильное приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

* Включите в приложение **собственный клиент**.
* Скопируйте **идентификатор приложения** , назначенный приложению. Этот идентификатор потребуется позднее.
* Настройте **URI перенаправления** собственного клиента (например, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Этот идентификатор также потребуется позднее.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Создание политик

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Это приложение предусматривает одну процедуру идентификации, сочетающую в себе вход и регистрацию. Вам нужно создать эту политику, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). При создании политики обязательно сделайте следующее:

* Укажите **отображаемое имя** в качестве атрибута входа для политики.
* Выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта** для каждой политики. Можно также выбрать другие утверждения.
* Скопируйте **имя** каждой созданной политики. У него должен быть префикс `b2c_1_`.  Эти имена политик понадобятся вам позже.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

После создания политик можно приступать к сборке приложения.

## <a name="download-the-sample-code"></a>Скачивание примера кода

Мы разместили рабочий пример, использующий AppAuth с Azure AD B2C, [на сайте GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Вы можете скачать код и запустить его. Вы можете быстро приступить к работе с приложением с использованием конфигурации Azure AD B2C, следуя инструкциям в разделе [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Пример представляет собой измененную версию примера, предоставляемого [AppAuth](https://openid.github.io/AppAuth-Android/). Посетите нашу страницу для получения дополнительных сведений об AppAuth и его функциях.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Изменение приложения для использования Azure AD B2C с AppAuth

> [!NOTE]
> AppAuth поддерживает Android API версии 16 (Jellybean) и более поздних. Мы советуем использовать API версии 23 и более поздних.
>

### <a name="configuration"></a>Конфигурация

Чтобы настроить взаимодействие с Azure AD B2C, укажите URI обнаружения или URI конечных точек авторизации и токенов. В любом случае вам потребуются следующие сведения:

* идентификатор клиента (например, contoso.onmicrosoft.com);
* имя политики (например, B2C\_1\_SignUpIn).

Если выбрано автоматическое обнаружение URI конечных точек авторизации и токенов, вам потребуется получить сведения из URI обнаружения. URI обнаружения можно создать, заменив идентификатор \_клиента и имя\_политики в следующем URL-адресе:

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Вы можете получить URI конечных точек авторизации и токенов, а также создать объект AuthorizationServiceConfiguration, выполнив следующую команду:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Вместо того, чтобы получать URI конечных точек авторизации и токенов путем обнаружения, их можно также явно определить, заменив идентификатор\_клиента и имя\_политики в приведенном ниже URL-адресе:

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Выполните следующий код для создания объекта AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Авторизация

После настройки или извлечения конфигурации службы авторизации можно сформировать запрос авторизации. Для создания запроса вам потребуются следующие сведения:

* идентификатор клиента (например, 00000000-0000-0000-0000-000000000000);
* URI перенаправления с настраиваемой схемой (например, com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect).

Оба элемента нужно сохранить при [регистрации приложения](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Дополнительные сведения о завершении остальной части процесса см. в [руководстве по AppAuth](https://openid.github.io/AppAuth-Android/). Если вам нужно быстро приступить к работе с приложением, ознакомьтесь с [нашим примером](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Следуйте указаниям в файле [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md), чтобы ввести собственные значения для настройки Azure AD B2C.

Мы всегда рады вашим отзывам и предложениям! Если у вас возникли трудности с выполнением действий, описанных в этой статье или у вас есть рекомендации по улучшению этого материала, поделитесь с нами своими наблюдениями, воспользовавшись формой в нижней части страницы. Запросы функций оставляйте на форуме [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).


