---
title: "Приступая к работе с Azure AD для Android | Документация Майкрософт"
description: "Практическое руководство по созданию приложения для Android, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, по протоколу OAuth."
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mbaldwin
editor: 
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 746cad19093fd2a1ad23ddd9412394f8d9da331c
ms.contentlocale: ru-ru
ms.lasthandoff: 07/12/2017

---
# <a name="integrate-azure-ad-into-an-android-app"></a>Интеграция Azure AD в приложение для Android
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

> [!TIP]
> Воспользуйтесь предварительной версией нашего нового [портала разработчиков](https://identity.microsoft.com/Docs/Android), который поможет вам приступить к работе с Azure AD через несколько минут. Портал разработчиков поможет зарегистрировать приложение и интегрировать Azure AD в коде. Завершив работу, вы получите простое приложение, с помощью которого выполняется проверка подлинности пользователей в клиенте и на сервере, принимающем маркеры и проводящем проверку.
>
>

При разработке классического приложения служба Azure Active Directory позволяет разработчику упростить аутентификацию локальных учетных записей пользователей в Active Directory. Это также позволяет вашему приложению безопасно использовать любые веб-интерфейсы API, защищаемые с помощью Azure AD, например интерфейсы Office 365 API или Azure API.

Клиентские приложения для Android, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL). Единственное предназначение ADAL — упростить процесс получения маркеров доступа. Чтобы показать, насколько это просто, мы создадим приложение To-Do List для Android, которое:

* получает маркеры доступа для вызова интерфейса API приложения To-Do List с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx);
* получает список дел пользователя;
* выполняет выход из системы для пользователей.

Чтобы приступить к работе, потребуется клиент Azure AD, в котором можно создавать пользователей и регистрировать приложение. Если клиента нет, [узнайте, как его получить](active-directory-howto-tenant.md).

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Этап 1. Скачивание и запуск образца службы, реализующей интерфейс REST API приложения To Do List для Node.js
Описываемый в этой статье образец специально создается так, чтобы можно было сравнить его работу с существующим образцом, создающим интерфейс REST API для однотенантного приложения To Do List под управлением Azure AD. Вышеупомянутый образец службы необходим для данного примера быстрого старта.

Сведения о том, как выполнить такую настройку, см. в существующих примерах в статье [Приступая к работе с веб-интерфейсом API для узла](active-directory-devquickstarts-webapi-nodejs.md).


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>Этап 2. Регистрация веб-API с помощью клиента Azure AD
Служба каталогов Active Directory позволяет добавлять приложения двух типов:

- веб-интерфейсы API, которые предоставляют услуги пользователям,
- и приложения (работающие через Интернет или на устройстве), использующие эти веб-API.

На этом этапе вы зарегистрируете веб-API, который будет работать локально для тестирования данного образца. Обычно этот веб-API является службой REST, предоставляющей приложению необходимую функциональность. Azure AD может помочь защитить любую конечную точку.

Предполагается, что вы будете регистрировать пример To Do REST API, упомянутый ранее. Но это работает для любого веб-API, который требуется защитить с помощью Azure Active Directory.

1. Войдите на [портал Azure](https://portal.azure.com).
2. На верхней панели щелкните свою учетную запись. В списке **Каталог** выберите клиент Azure AD, в котором будет зарегистрировано приложение.
3. В области слева щелкните **Больше служб** и выберите **Azure Active Directory**.
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

1. Войдите на [портал Azure](https://portal.azure.com).
2. На верхней панели щелкните свою учетную запись. В списке **Каталог** выберите клиент Azure AD, в котором будет зарегистрировано приложение.
3. В области слева щелкните **Больше служб** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.
5. Введите понятное имя для приложения, например **TodoListService**, выберите **Собственное клиентское приложение** и нажмите кнопку **Далее**.
6. В качестве URI переадресации введите `http://TodoListClient`. Нажмите кнопку **Готово**
7. На странице приложения найдите значение идентификатора приложения и скопируйте его. Оно понадобится позже при настройке приложения.
8. На странице **Параметры** выберите **Необходимые разрешения** и щелкните **Добавить**.  Выберите TodoListService, добавьте разрешение **Access TodoListService** (Доступ к TodoListService) в списке **Делегированные разрешения** и щелкните **Готово**.

Для сборки с помощью Maven можно использовать файл pom.xml на верхнем уровне.

1. Клонируйте этот репозиторий в любой каталог по своему усмотрению:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  
2. Выполните действия, описанные в [предварительных требованиях для настройки среды Maven для Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
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
Чтобы скачать копию исходного кода, в правой части страницы щелкните **Download ZIP** (Загрузить ZIP). Или [скачайте его с GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

### <a name="option-2-source-via-git"></a>Способ 2: исходные файлы через Git
Чтобы получить исходный код пакета SDK через Git, введите:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
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

4. Создайте экземпляр класса AuthenticationContext с вашим MainActivity. Описание этого вызова выходит за рамки данной статьи, но вы можете получить дополнительные сведения в разделе с [образцом собственного клиентского приложения для Android](https://github.com/AzureADSamples/NativeClient-Android). Ниже приведен пример, где SharedPreferences используется как кэш по умолчанию и права представлены в виде `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`:

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
### <a name="customization"></a>Настройка
Ресурсы проекта библиотеки могут быть перезаписаны ресурсами вашего приложения. Перезапись происходит при сборке приложения. Поэтому вы можете настроить макет AuthenticationActivity так, как требуется. Необходимо обязательно сохранять идентификаторы элементов управления, которые используются библиотекой ADAL (веб-представление).

### <a name="broker"></a>Broker
Компонент broker предоставляется приложением корпоративного портала Microsoft Intune. Учетная запись создается в диспетчере учетных записей. Тип учетной записи — com.microsoft.workaccount. Диспетчер учетных записей разрешает только одну учетную запись единого входа. Этот компонент создает файл cookie единого входа для данного пользователя после завершения запроса устройства одним из приложений.

Библиотека ADAL использует учетную запись broker, если существует одноименная пользовательская учетная запись, созданная в этой структуре проверки подлинности, и вы решили не игнорировать ее. Вы можете пропустить пользователя broker с помощью следующей команды:

   `AuthenticationSettings.Instance.setSkipBroker(true);`

Для использования broker необходимо зарегистрировать специальный URI перенаправления. URI перенаправления имеет формат `msauth://packagename/Base64UrlencodedSignature`. Можно получить URI перенаправления для приложения с помощью скрипта brokerRedirectPrint.ps1 или с помощью вызова метода API mContext.getBrokerRedirectUri. Подпись связана с вашими сертификатами подписи.

Текущая модель broker предназначена для одного пользователя. Класс AuthenticationContext предоставляет метод API для получения пользователя broker:

   `String brokerAccount =  mContext.getBrokerUser(); //Broker user is returned if account is valid.`

Манифест приложения должен иметь следующие разрешения на использование учетных записей в диспетчере учетных записей. Дополнительные сведения см. на странице [AccountManager на сайте Android](http://developer.android.com/reference/android/accounts/AccountManager.html).

* GET_ACCOUNTS
* USE_CREDENTIALS
* MANAGE_ACCOUNTS

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

#### <a name="exceptions"></a>Исключения
Исключения — это первый этап диагностики. Мы стараемся предоставить содержательные сообщения об ошибках. Если вы не считаете какое-либо сообщение содержательным, пожалуйста, сообщите нам об этом. Укажите такие сведения об устройстве, как модель и версия SDK.

#### <a name="logs"></a>Журналы
Можно настроить библиотеку на создание сообщений журнала, которые будут использоваться для диагностики проблем. Чтобы настроить ведение журнала, используйте следующую функцию обратного вызова, которая будет вызываться библиотекой ADAL для передачи сообщений журнала при их создании.

    Logger.getInstance().setExternalLogger(new ILogger() {
        @Override
        public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
        ...
        // You can write this to log file depending on level or error code.
        writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
        }
    }

Сообщения могут записываться в файл пользовательского журнала, как показано ниже. К сожалению, нет стандартного способа получения журналов с устройств. Существуют некоторые службы, которые могут в этом помочь. Также вы можете создать свою службу, например для отправки файла на сервер.

    private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
    }

Уровни протоколирования:
* Error (исключение);
* Warn (предупреждение);
* Info (информирование);
* Verbose (дополнительные сведения).

Настройка уровня ведения журнала происходит следующим образом.

    Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

 Все сообщения журнала передаются в logcat в дополнение ко всем обратным вызовам пользовательского журнала.
Ниже показано, как получить файл журнала из logcat.

    adb logcat > "C:\logmsg\logfile.txt"

 Дополнительные сведения о командах adb см. на [странице сведений о logcat на сайте Android](https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat).

#### <a name="network-traces"></a>Данные трассировки сети
Для перехвата HTTP-трафика, который создает библиотека ADAL, можно использовать различные инструменты.  Это очень полезно, если вы знакомы с протоколом OAuth или если необходимо предоставить диагностические сведения в корпорацию Майкрософт или другие каналы поддержки.

Fiddler является простым инструментом трассировки HTTP. Используйте следующие ссылки для получения информации о правильной настройке инструмента Fiddler для записи сетевого трафика библиотеки ADAL. Чтобы достичь положительного результата, средство трассировки, например Fiddler или Charles, должно быть настроено для записи незашифрованного трафика SSL.  

> [!NOTE]
> Данные трассировки, записанные таким образом, могут содержать конфиденциальные сведения, такие как маркеры доступа, имена пользователей и пароли. При использовании рабочих учетных записей не передавайте данные трассировки третьим сторонам. Если для получения поддержки необходимо предоставить данные трассировки другому пользователю, воспроизведите проблему с временной учетной записью, используя имена пользователей и пароли, которые не будут использоваться для реальных пользователей.

* Использование веб-сайта компании Telerik: [Настройка Fiddler для Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
* Использование GitHub : [Настройка правил Fiddler для библиотеки ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)

### <a name="dialog-mode"></a>Режим диалогового окна
Метод acquireToken с нулевым первым параметром приведет к отображению диалогового окна.

### <a name="encryption"></a>Шифрование
ADAL шифрует маркеры и по умолчанию хранит их в SharedPreferences. Вы можете подробно рассмотреть класс StorageHelper. В Android 4.3 (API уровня 18) введено безопасное хранилище закрытых ключей AndroidKeyStore. Библиотека ADAL использует это хранилище при работе с API уровня 18 и выше. Если требуется использовать ADAL с более ранними версиями SDK, необходимо предоставить секретный ключ в AuthenticationSettings.INSTANCE.setSecretKey.

### <a name="oauth2-bearer-challenge"></a>Запрос носителя OAuth2
Класс AuthenticationParameters предоставляет функциональные возможности для получения authorization_uri из запроса носителя OAuth2.

### <a name="session-cookies-in-webview"></a>Файлы cookie сеанса в веб-представлении
После закрытия приложения веб-представление Android не очищает файлы cookie сеанса. Это можно сделать с помощью следующего примера кода.

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

Дополнительные сведения о файлах cookie см. на [странице сведений о CookieSyncManager на сайте Android](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

### <a name="resource-overrides"></a>Переопределения ресурсов
Библиотека ADAL содержит строки на английском языке для элементов пользовательского интерфейса диалоговых окон. Разработчику следует перезаписать эти сроки, если требуется их локализовать.

     <string name="app_loading">Loading...</string>
     <string name="broker_processing">Broker is processing</string>
     <string name="http_auth_dialog_username">Username</string>
     <string name="http_auth_dialog_password">Password</string>
     <string name="http_auth_dialog_title">Sign In</string>
     <string name="http_auth_dialog_login">Login</string>
     <string name="http_auth_dialog_cancel">Cancel</string>

### <a name="ntlm-dialog-box"></a>Диалоговое окно NTLM
Версия ADAL 1.1.0 поддерживает диалоговое окно NTLM, которое отображается по событию onReceivedHttpAuthRequest, поступающему из WebViewClient. Макет и строки диалогового окна можно настроить.

### <a name="cross-app-sso"></a>Единый вход для нескольких приложений
Узнайте, [как включить единый вход для нескольких приложений Android с помощью ADAL](active-directory-sso-android.md).  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

