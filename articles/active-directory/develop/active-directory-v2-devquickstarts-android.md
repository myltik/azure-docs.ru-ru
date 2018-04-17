---
title: "Приложение Android версии 2.0 для Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как создать приложение Android, которое обеспечивает вход пользователей с помощью личной и рабочей или учебной учетной записи Майкрософт, а также использует вызовы API Graph на основе сторонних библиотек."
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mtillman
editor: 
ms.assetid: 16294c07-f27d-45c9-833f-7dbb12083794
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: b1c30362a7b14c8f7f0c44d911c46c491b3de3c0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Добавление функции входа в приложение Android, использующее стороннюю библиотеку и API Graph, с помощью конечной точки версии 2.0
Платформа Microsoft Identity использует открытые стандарты, такие как OAuth2 и OpenID Connect. Разработчики могут использовать любую библиотеку на свой выбор для интеграции с нашими службами. Чтобы помочь разработчикам с использованием нашей платформы с другими библиотеками, мы написали несколько подобных этому пошаговых руководств по настройке сторонних библиотеки для подключения к платформе Microsoft Identity. Большинство библиотек, в которых реализована [спецификация RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) , могут подключаться к платформе Microsoft Identity.

Используя приложение, создаваемое в этом пошаговом руководстве, пользователи смогут войти в инфраструктуру своей организации и найти там себя с помощью API Graph.

Если вы еще не работали с OAuth2 или OpenID Connect, то вы не поймете большую часть примера конфигурации. Мы рекомендуем прочитать раздел [Протоколы версии 2.0 — поток кода авторизации OAuth 2.0](active-directory-v2-protocols-oauth-code.md), чтобы получить соответствующие сведения.

> [!NOTE]
> Чтобы использовать функции платформы, которые описаны в этих стандартах OAuth2 или OpenID Connect, такие как управление политиками условного доступа и управление политиками Intune, требуются библиотеки Microsoft Azure Identity с открытым кодом.
> 
> 

Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0.

> [!NOTE]
> Чтобы определить, следует ли вам использовать конечную точку 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-the-code-from-github"></a>Скачивание кода с сайта GitHub
Код в этом учебнике размещен на портале [GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) или клонировать ее.

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Можно также просто скачать пример и немедленно приступить к работе.

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>регистрация приложения;
Создайте новое приложение на [портале регистрации приложений](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) или выполните подробные инструкции по [регистрации приложения с использованием конечной точки версии 2.0](active-directory-v2-app-registration.md).  Не забудьте:

* Скопируйте назначенный вашему приложению **идентификатор приложения**. Он вскоре вам понадобится.
* Добавьте для приложения **мобильную** платформу.

> Примечание. Портал регистрации приложений предоставляет значение **URI перенаправления**. Однако в этом примере необходимо использовать значение по умолчанию `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 
> 

## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Скачивание сторонней библиотеки NXOAuth2 и создание рабочей области
Для этого пошагового руководства вы воспользуетесь библиотекой OIDCAndroidLib с сайта GitHub. Это библиотека OAuth2, созданная на основе кода OpenID Connect от Google. Она реализует профиль собственного приложения и поддерживает конечную точку авторизации пользователей. Это все, что вам потребуется для интеграции с платформой Microsoft Identity.

Клонируйте репозиторий OIDCAndroidLib на свой компьютер.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](../media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Настройка среды Android Studio
1. Создайте новый проект Android Studio и примите значения по умолчанию в мастере.
   
    ![Создание нового проекта в Android Studio](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)
   
    ![Целевые устройства Android](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)
   
    ![Добавление действия для мобильных устройств](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)
2. Чтобы установить модули проекта, переместите клонированный репозиторий в расположение проекта. Можно также сначала создать проект, а затем клонировать его непосредственно в расположение проекта.
   
    ![Модули проекта](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)
3. Откройте параметры модулей проекта с помощью контекстного меню или нажав клавиши CTRL+ALT+SHIFT+S.
   
    ![Параметры модулей проекта](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)
4. Удалите модуль приложения по умолчанию, так как нам нужны только параметры контейнера проекта.
   
    ![Модуль приложения по умолчанию](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)
5. Импортируйте модули из клонированного репозитория в текущий проект.
   
    ![Импорт проекта gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![Страница создания модуля](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)
6. Повторите эти действия для модуля `oidlib-sample` .
7. Проверьте зависимости oidclib в модуле `oidlib-sample` .
   
    ![Зависимости oidclib в модуле oidlib-sample](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)
8. Нажмите кнопку **ОК** и дождитесь завершения синхронизации с Gradle.
   
    Раздел settings.gradle должен выглядеть следующим образом.
   
    ![Снимок экрана settings.gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)
9. Выполните сборку примера приложения, чтобы убедиться, что он работает правильно.
   
    Вы пока не сможете использовать этот пример с Azure Active Directory. Сначала необходимо настроить некоторые конечные точки. Так еще до начала настройки примера приложения мы сможем убедиться, что с Android Studio не возникают проблемы.
10. Выполните сборку `oidlib-sample` и запустите его в Android Studio в качестве целевого приложения.
    
    ![Ход выполнения сборки oidlib-sample](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)
11. Удалите каталог `app ` , который остался после удаления модуля из проекта, так как в целях безопасности Android Studio не удаляет его.
    
    ![Структура файлов, содержащая каталог приложения](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)
12. Откройте меню **Изменить конфигурации** , чтобы удалить конфигурацию запуска, которая также осталась после удаления модуля из проекта.
    
    ![Меню "Изменить конфигурации"](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![Конфигурация запуска приложения](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Настройка конечных точек примера
После успешного запуска `oidlib-sample` можно приступить к изменению некоторых конечных точек для их подключения к Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Настройка клиента посредством редактирования файла oidc_clientconf.xml
1. Так как для получения токена и вызова API Graph используются потоки OAuth2, настроим для клиента только протокол OAuth2. В примере, который станет доступен позже, мы будем использовать OIDC.
   
    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```
2. Настройте идентификатор клиента, полученный с портала регистрации.
   
    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```
3. Настройте универсальный код ресурса (URI) перенаправления, указав приведенное ниже значение.
   
    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```
4. Настройте области, необходимые для доступа к API Graph.
   
    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

Значение `User.Read` в `oidc_scopes` позволяет считывать базовый профиль вошедшего пользователя.
Дополнительные сведения о всех доступных областях действия см. в разделе [Microsoft Graph permission scopes](https://graph.microsoft.io/docs/authorization/permission_scopes) (Области действия разрешений Microsoft Graph).

Пояснения об использовании `openid` или `offline_access` в качестве областей действия в OpenID Connect см. в разделе [Протоколы версии 2.0 — поток кода авторизации OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Настройка конечных точек клиента посредством редактирования файла oidc_endpoints.xml
* Откройте файл `oidc_endpoints.xml` и внесите в него следующие изменения.
  
    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

Если используется протокол OAuth2, эти конечные точки никогда не должны изменяться.

> [!NOTE]
> В настоящее время конечные точки для `userInfoEndpoint` и `revocationEndpoint` не поддерживаются в Azure Active Directory. Если оставить значение по умолчанию example.com, то появится напоминание о том, в примере они недоступны.
> 
> 

## <a name="configure-a-graph-api-call"></a>Настройка вызова API Graph
* Откройте файл `HomeActivity.java` и внесите в него следующие изменения.
  
    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

Здесь простой вызов API Graph возвращает нашу информацию.

Это все изменения, которые необходимо выполнить. Запустите приложение `oidlib-sample` и щелкните **Sign in**(Войти).

После аутентификации нажмите кнопку **Request Protected Resource** (Запросить защищенный ресурс), чтобы проверить вызов к API Graph.

## <a name="get-security-updates-for-our-product"></a>Получение обновлений системы безопасности для наших продуктов
Рекомендуем вам получать уведомления об инцидентах безопасности. Для этого посетите [Технический центр безопасности](https://technet.microsoft.com/security/dd252948) и подпишитесь на уведомления о советах безопасности.

