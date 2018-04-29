---
title: Приступая к работе с Azure AD для Android | Документация Майкрософт
description: Практическое руководство по созданию приложения для Android, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, с помощью протокола OAuth2.0.
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/30/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 25a908c542bf8fdd8008841a1865cdfb40d847fc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-android-getting-started"></a>Начало работы с Azure AD для Android
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

При разработке классического приложения служба Azure Active Directory позволяет разработчику упростить аутентификацию локальных учетных записей пользователей в Active Directory. Это также позволяет вашему приложению безопасно использовать любые веб-интерфейсы API, защищаемые с помощью Azure AD, например интерфейсы Office 365 API или Azure API.

Клиентские приложения для Android, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL). Единственное предназначение ADAL — упростить процесс получения маркеров доступа. Чтобы показать, насколько это просто, мы создадим приложение To-Do List для Android, которое:

* получает маркеры доступа для вызова API-интерфейса приложения To Do List с помощью [протокола проверки подлинности OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code);
* получает список дел пользователя;
* выполняет выход из системы для пользователей.

Чтобы приступить к работе, потребуется клиент Azure AD, в котором можно создавать пользователей и регистрировать приложение. Если клиента нет, [узнайте, как его получить](active-directory-howto-tenant.md).

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Этап 1. Скачивание и запуск образца службы, реализующей интерфейс REST API приложения To Do List для Node.js
Описываемый в этой статье образец специально создается так, чтобы можно было сравнить его работу с существующим образцом, создающим интерфейс REST API для однотенантного приложения To Do List под управлением Azure AD. Вышеупомянутый образец службы необходим для данного примера быстрого старта.

Сведения о том, как выполнить такую настройку, см. в существующих примерах в статье [Приступая к работе с веб-API Node.js для Azure AD](active-directory-devquickstarts-webapi-nodejs.md).


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>Этап 2. Регистрация веб-API с помощью клиента Azure AD
Служба каталогов Active Directory позволяет добавлять приложения двух типов:

- веб-интерфейсы API, которые предоставляют услуги пользователям,
- и приложения (работающие через Интернет или на устройстве), использующие эти веб-API.

На этом этапе вы зарегистрируете веб-API, который будет работать локально для тестирования данного образца. Обычно этот веб-API является службой REST, предоставляющей приложению необходимую функциональность. Azure AD может помочь защитить любую конечную точку.

Предполагается, что вы будете регистрировать пример To Do REST API, упомянутый ранее. Но это работает для любого веб-API, который требуется защитить с помощью Azure Active Directory.

1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели щелкните свою учетную запись. В списке **Каталог** выберите клиент Azure AD, в котором будет зарегистрировано приложение.
3. В области слева щелкните **Все службы** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.
5. Введите понятное имя для приложения, например **TodoListService**, выберите **Веб-приложение и/или веб-API** и нажмите кнопку **Далее**.
6. В качестве URL-адреса единого входа введите базовый URL-адрес для образца. По умолчанию это `https://localhost:8080`.
7. Для завершения регистрации нажмите кнопку **ОК**.
8. Оставаясь на портале Azure, перейдите на страницу приложения, найдите значение идентификатора и скопируйте его. Оно понадобится позже при настройке приложения.
9. На странице **Параметры** -> **Свойства** измените универсальный код ресурса (URI) идентификатора приложения, введя `https://<your_tenant_name>/TodoListService`. Замените `<your_tenant_name>` именем вашего клиента Azure AD.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Этап 3. Регистрация образца нативного клиентского приложения для Android
В этом примере необходимо зарегистрировать веб-приложение. Это позволяет приложению взаимодействовать с только что зарегистрированным веб-API. Если приложение не зарегистрировано, то Azure AD не позволит ему даже запросить данные для входа. Это часть модели безопасности.

Предполагается, что вы регистрируете пример приложения, упомянутый ранее. Но эта процедура подходит для любого приложения, которое вы разрабатываете.

> [!NOTE]
> Может возникнуть вопрос, почему приложение и веб-интерфейс API размещаются в одном клиенте? Как вы могли догадаться, можно создать приложение, которое обращается к внешнему интерфейсу API, зарегистрированному в Azure AD из другого клиента. При этом пользователям будет предложено дать согласие на использование этого интерфейса API в приложении. Библиотека проверки подлинности Active Directory для iOS выполняет все необходимые операции для обработки данного согласия. В более сложных задачах становится очевидным, что обработка согласия является важной составляющей доступа к интерфейсам Microsoft API от Azure и Office, а также любого другого поставщика услуг. Пока приложение и веб-интерфейс зарегистрированы в рамках одного и того же клиента, пользователь не увидит запросы на согласие. Это типично для приложений, предназначенных для использования только внутри компании.

1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели щелкните свою учетную запись. В списке **Каталог** выберите клиент Azure AD, в котором будет зарегистрировано приложение.
3. В области слева щелкните **Все службы** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.
5. Введите понятное имя для приложения, например **TodoListService**, выберите **Собственное клиентское приложение** и нажмите кнопку **Далее**.
6. В качестве URI переадресации введите `http://TodoListClient`. Нажмите кнопку **Готово**
7. На странице приложения найдите значение идентификатора приложения и скопируйте его. Оно понадобится позже при настройке приложения.
8. На странице **Параметры** выберите **Необходимые разрешения** и щелкните **Добавить**.  Выберите TodoListService, добавьте разрешение **Access TodoListService** (Доступ к TodoListService) в списке **Делегированные разрешения** и щелкните **Готово**.

Для сборки с помощью Maven можно использовать файл pom.xml на верхнем уровне.

1. Клонируйте этот репозиторий в любой каталог по своему усмотрению:

  `$ git clone https://github.com/Azure-Samples/active-directory-android.git`  
2. Выполните действия, описанные в [предварительных требованиях для настройки среды Maven для Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Maven).
3. Установите эмулятор с пакетом SDK 19.
4. Перейдите в корневую папку, куда клонирован репозиторий.
5. Выполните следующую команду: `mvn clean install`
6. Перейдите в каталог примера краткого руководства `cd samples\hello`.
7. Выполните следующую команду: `mvn android:deploy android:run`

   Приложение будет запущено.
8. Введите учетные данные тестового пользователя.

JAR-пакеты будут также отправляться наряду с AAR-пакетом.

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Этап 4. Скачивание библиотеки ADAL для Android и добавление ее в рабочую область платформы Eclipse
Существует несколько способов использования библиотеки ADAL в проекте для Android:

* Можно импортировать библиотеку в платформу Eclipse в виде исходного кода и связать с приложением.
* В Android Studio можно использовать пакет AAR и сослаться на двоичные файлы.

### <a name="option-1-source-zip"></a>Способ 1: ZIP-архив с файлами исходного кода
Чтобы скачать копию исходного кода, в правой части страницы щелкните **Download ZIP** (Загрузить ZIP). Или [скачайте его с GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android/releases).

### <a name="option-2-source-via-git"></a>Способ 2: исходные файлы через Git
Чтобы получить исходный код пакета SDK через Git, введите:

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>Способ 3: двоичные файлы через Gradle
Двоичные файлы можно получить из центрального репозитория Maven. Пакет AAR может быть включен в проект Android Studio следующим образом:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>Способ 4: AAR через Maven
Если используется подключаемый модуль M2Eclipse, можно задать зависимость в файле pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>Способ 5: JAR-пакет в папке libs
Можно перетащить JAR-файл из репозитория Maven в папку **libs** проекта. Вы должны скопировать требуемые ресурсы в свой проект, так как они не включены в JAR-пакеты.

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>Этап 5: добавление ссылки на библиотеку ADAL для Android в проект
1. Добавьте в проект ссылку на библиотеку для Android. Если вы не знаете, как это сделать, ознакомьтесь с дополнительными сведениями на [сайте Android Studio](http://developer.android.com/tools/projects/projects-eclipse.html).
2. Добавьте в параметры проекта зависимость проекта для отладки.
3. Добавьте в файл проекта AndroidManifest.xml следующее:

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. Создайте экземпляр класса AuthenticationContext с вашим MainActivity. Описание этого вызова выходит за рамки данной статьи, но вы можете получить дополнительные сведения в разделе с [образцом собственного клиентского приложения для Android](https://github.com/AzureAD/azure-activedirectory-library-for-android). Ниже приведен пример, где SharedPreferences используется как кэш по умолчанию и права представлены в виде `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`:

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. Скопируйте этот блок кода для обработки в конец AuthenticationActivity после фрагмента, где пользователь вводит учетные данные и получает код авторизации:

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. Чтобы запросить маркер, определите функцию обратного вызова:

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. Наконец, запросите маркер с помощью данного обратного вызова:

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

Объяснение параметров:

* Параметр *resource* является обязательным и указывает ресурс, к которому требуется получить доступ.
* Параметр *clientId* является обязательным и поступает из Azure AD.
* Параметр *RedirectUri* не требуется для вызова acquireToken. В качестве него можно задать имя пакета.
* Параметр *PromptBehavior* указывает на необходимость запроса учетных данных, а не получения их из кэша или файла cookie.
* Параметр *callback* вызывается после обмена кода авторизации на маркер. Он получает объект AuthenticationResult, который имеет маркер доступа, дату окончания срока действия и сведения об идентификаторе маркера.
* Параметр *acquireTokenSilent* является необязательным. Его можно вызвать для обработки кэшированных данных и обновления маркеров. Также данный метод обеспечивает возможность выполнить синхронный вызов. Он принимает *userid* в качестве параметра.

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

Данное пошаговое руководство предоставляет все необходимое для успешной интеграции с Azure AD. Дополнительные примеры представлены в репозитории AzureADSamples/ на портале GitHub.

## <a name="important-information"></a>Важные сведения

### <a name="broker"></a>Broker
Компонент-брокер предоставляется приложением "Корпоративный портал Intune" или приложением Microsoft Authenticator. Учетная запись создается в диспетчере учетных записей. Тип учетной записи — com.microsoft.workaccount. Диспетчер учетных записей разрешает только одну учетную запись единого входа. Этот компонент создает файл cookie единого входа для данного пользователя после завершения запроса устройства одним из приложений.

Дополнительные сведения о настройке с использованием брокера см. в этой [вики-статье](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Broker). 

### <a name="authority-url-and-ad-fs"></a>URL-адрес центра и службы федерации Active Directory
Службы федерации Active Directory не считаются службой маркеров безопасности, поэтому необходимо включить обнаружение экземпляра службы и передать значение false в конструктор AuthenticationContext.

URL-адресу центра необходим экземпляр службы маркеров безопасности и [имя клиента](https://login.microsoftonline.com/yourtenant.onmicrosoft.com).

### <a name="querying-cache-items"></a>Запрос элементов кэша
Библиотека ADAL предоставляет кэш по умолчанию в SharedPrefrecens и несколько простых функций для запроса данных из кэша. Вот как можно получить текущий кэш из AuthenticationContext.

    ITokenCacheStore cache = mContext.getCache();

Кроме того, можно предоставить собственную реализацию кэша, если вы хотите его настроить.

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>Поведения запроса
ADAL предоставляет возможность указать, требуется ли отображать диалоговое окно. При указании параметра PromptBehavior.Auto будет отображено диалоговое окно, только если маркер обновления недействителен и требуются учетные данные пользователя. При указании параметра PromptBehavior.Always данные кэша игнорируются, и диалоговое окно запроса учетных данных будет отображаться всегда.

### <a name="silent-token-request-from-cache-and-refresh"></a>Автоматический запрос маркера из кэша и обновление
Автоматический запрос маркера не использует диалоговое окно запроса учетных данных и не требует от пользователя каких-либо действий. Он возвращает маркер из кэша, если тот доступен. Если срок действия маркера истек, метод попытается обновить его. Если срок действия маркера обновления истек или маркер ошибочен, метод вызовет исключение AuthenticationException.

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

Также данный метод обеспечивает возможность выполнить синхронный вызов. Для параметра callback используйте acquireTokenSilentSync или задайте значение null.

### <a name="diagnostics"></a>Диагностика
Ниже представлены основные источники информации для диагностики проблем.

* Исключения
* Журналы
* Данные трассировки сети

Следует отметить, что в библиотеке наиболее важными для диагностики являются идентификаторы корреляции. Вы можете установить собственные идентификаторы корреляции для отдельных запросов, если требуется сопоставить запрос ADAL с другими операциями в коде. Если идентификатор корреляции не задан, то библиотека ADAL создает случайный идентификатор. Все сообщения журнала и сетевые вызовы будут помечены этим идентификатором корреляции. Генерируемый идентификатор изменяется при каждом запросе.

#### <a name="errors--exceptions"></a>Ошибки и исключения
Исключения — это первый этап диагностики. Мы стараемся предоставить содержательные сообщения об ошибках. Если вы не считаете какое-либо сообщение содержательным, пожалуйста, сообщите нам об этом. Укажите такие сведения об устройстве, как модель и версия SDK.

Дополнительные сведения о том, какие исключения должно обрабатывать приложение, см. в статье [Руководство по обработке ошибок для клиентов библиотеки аутентификации Active Directory (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-adal-error-handling). 

#### <a name="logs"></a>Журналы
Можно настроить библиотеку на создание сообщений журнала, которые будут использоваться для диагностики проблем. Чтобы настроить ведение журнала, используйте следующую функцию обратного вызова, которая будет вызываться библиотекой ADAL для передачи сообщений журнала при их создании.

Чтобы включить ведение журнала, см. эту [вики-статью](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Logging).

### <a name="session-cookies-in-webview"></a>Файлы cookie сеанса в веб-представлении
После закрытия приложения веб-представление Android не очищает файлы cookie сеанса. Это можно сделать с помощью следующего примера кода.

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

Дополнительные сведения о файлах cookie см. на [странице сведений о CookieSyncManager на сайте Android](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

### <a name="ntlm-dialog-box"></a>Диалоговое окно NTLM
Версия ADAL 1.1.0 поддерживает диалоговое окно NTLM, которое отображается по событию onReceivedHttpAuthRequest, поступающему из WebViewClient. Макет и строки диалогового окна можно настроить.

### <a name="cross-app-sso"></a>Единый вход для нескольких приложений
Узнайте, [как включить единый вход для нескольких приложений Android с помощью ADAL](active-directory-sso-android.md).  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
