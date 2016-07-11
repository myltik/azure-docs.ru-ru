<properties
	pageTitle="Приложение Android версии 2.0 для Azure AD | Microsoft Azure"
	description="Узнайте, как создать приложение Android, которое поддерживает вход пользователей в систему с помощью личной и рабочей или учебной учетной записи Майкрософт, а также как создать вызовы API Graph, используя сторонние библиотеки."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="brandwe"/>

#  Добавление функции входа в приложение Android, использующее стороннюю библиотеку и API Graph, с помощью конечной точки версии 2.0

Платформа Microsoft Identity использует открытые стандарты, такие как OAuth2 и OpenID Connect. Это позволяет разработчикам использовать для интеграции со службами любые библиотеки. Чтобы объяснить разработчикам, как настроить сторонние библиотеки для подключения к платформе Microsoft Identity, мы написали несколько подобных этому пошаговых руководств. Большинство библиотек, в которых реализована [спецификация RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749), смогут подключаться к платформе Microsoft Identity.

Это приложение позволяет пользователям входить в свою организацию и искать свой профиль с помощью API Graph.

Если вы еще не работали с OAuth2 или OpenID Connect, вы не поймете большую часть примера конфигурации. Для начала мы рекомендуем просмотреть [общие сведения о протоколе](active-directory-v2-protocols-oauth-code.md).


> [AZURE.NOTE]
	Чтобы использовать функции платформы, которые имеют выражения в этих стандартах, такие как управление политикой условного доступа и Intune, требуются библиотеки Microsoft Azure Identity с открытым исходным кодом.


> [AZURE.NOTE]
	Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).


## Загрузить
Код в этом учебнике размещен на портале [GitHub](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git). Для понимания процесса можно [скачать основу приложения как ZIP-файл](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git/archive/skeleton.zip) или клонировать ее:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Или просто скачайте код и немедленно приступите к работе.

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## регистрация приложения;
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com) или выполните [эти подробные указания](active-directory-v2-app-registration.md). Не забудьте:

- запишите назначенный вашему приложению **идентификатор**. Он вскоре вам понадобится.
- Добавьте для приложения **мобильную** платформу.
- Скопируйте с портала **универсальный код ресурса (URI) перенаправления**. Необходимо использовать стандартное значение `https://login.microsoftonline.com/common/oauth2/nativeclient`.


## Загрузка сторонней библиотеки nxoauth2 и запуск рабочей области

В этом пошаговом руководстве используется библиотека OIDCAndroidLib из GitHub, библиотека OAuth2, созданная на основе кода Google OpenID Connect. Она реализует профиль собственного приложения и поддерживает конечную точку авторизации конечных пользователей. Это все, что нам потребуется для интеграции с платформой Microsoft Identity.

*  Клонировать

Сначала клонируйте на свой компьютер репозиторий OIDCAndroidLib.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

### Настройка среды Android Studio

*  Создайте проект "Создать проект AndroidStudio" и выполните указания мастера настройки по умолчанию.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

*  Настройте модули проекта, переместив клонированный репозиторий в расположение проекта. Можно также сначала создать проект, а затем клонировать репозиторий непосредственно в папку проекта.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

Затем откройте в контекстном меню параметры модулей проекта или нажмите клавиши `Ctrl + Alt + Maj + S`.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

Удалите модуль приложения по умолчанию, так как нам нужны только параметры контейнера проекта.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

Теперь необходимо импортировать модули из клонированного репозитория в текущий проект.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

> Повторите эти действия для модуля `oidlib-sample`.

Проверьте зависимости oidclib в модуле `oidlib-sample`.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

Нажмите кнопку "ОК" и дождитесь завершения синхронизации с Gradle.

Параметры Gradle должны выглядеть следующим образом:

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

*  Создайте пример приложения, чтобы убедиться, что оно работает правильно.

Вы пока не сможете использовать этот пример с Azure Active Directory. Сначала необходимо настроить некоторые конечные точки. Так еще до начала настройки примера приложения мы сможем убедиться, что с Android Studio не возникают проблемы.

Создайте и запустите в Android Studio `oidlib-sample` в качестве целевого приложения.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

*  Очистка

Каталог `app `, который остался после удаления модуля из проекта, можно удалить самостоятельно, так как в целях безопасности Android Studio не удаляет его.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

В меню "Изменить конфигурации" можно удалить конфигурацию запуска, которая также осталась после удаления модуля из проекта.

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG) ![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## Настройка конечных точек примера

После успешного запуска `oidlib-sample` можно приступить к изменению некоторых конечных точек для их подключения к Azure Active Directory.

* Настройка клиента

Откройте файл `oidc_clientconf.xml` и внесите в него следующие изменения:

1. Так как для получения токена и вызова API Graph используются потоки OAuth2, настроим для клиента только протокол OAuth2. Позже в примере мы будем использовать OIDC.

```xml
    <bool name="oidc_oauth2only">true</bool>
```

2. Настройте идентификатор клиента, полученный с портала регистрации.

```xml
    <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
    <string name="oidc_clientSecret"></string>
```

3. Настройте универсальный код ресурса перенаправления, полученный с портала регистрации.

```xml
    <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
```

4. Настройте области, необходимые для доступа к API Graph.

```xml
    <string-array name="oidc_scopes">
        <item>openid</item>
        <item>User.ReadBasic.All</item>
        <item>offline_access</item>
    </string-array>
```

Здесь мы задаем значение `oidc_scopes`. Для данного приложения мы будем запрашивать область `User.ReadBasic.All`, которая позволяет просматривать базовые профили всех пользователей в каталоге. Дополнительные сведения об областях, доступных для использования с Microsft Graph, см. [здесь](https://graph.microsoft.io/docs/authorization/permission_scopes).

Чтобы получить дополнительные сведения об области `openid` или `offline_access` в OpenID Connect, см. [общие сведения о протоколе](active-directory-v2-protocols-oauth-code.md).

* Настройка конечных точек клиента

Откройте файл `oidc_endpoints.xml` и внесите в него следующие изменения:

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

> [AZURE.NOTE] 
На данный момент Azure Active Directory не поддерживает конечные точки `userInfoEndpoint` и `revocationEndpoint`, поэтому для них мы оставляем значения по умолчанию example.com, которые будут напоминать, что они недоступны в данном примере.


## Настройка вызова API Graph

Откройте файл `HomeActivity.java` и внесите в него следующие изменения:

```Java
   //TODO: set your protected resource url
    private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
```

В качестве примера мы создадим простой вызов API Graph, который вернет необходимые данные.

## Готово!

Мы внесли все необходимые изменения! Запустите приложение `oidlib-sample` и щелкните ссылку "Вход". O

После выполнения проверки подлинности нажмите кнопку Request Protected Resource (Запросить защищенный ресурс), чтобы проверить вызов к API Graph.

## Получение обновлений системы безопасности для наших продуктов

Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

<!---HONumber=AcomDC_0629_2016-->