<properties
	pageTitle="Приступая к работе с Azure AD для Android | Microsoft Azure"
	description="Практическое руководство по созданию приложения для Android, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, по протоколу OAuth."
	services="active-directory"
	documentationCenter="android"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# Интеграция Azure AD в приложение для Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

При разработке классического приложения служба Microsoft Azure Active Directory позволяет разработчику упростить аутентификацию учетных записей пользователей в Active Directory. Это также позволяет вашему приложению безопасно использовать любые веб-интерфейсы API, защищаемые с помощью Azure AD, например интерфейсы Office 365 API или Azure API.

Клиентские приложения для Android, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL). Единственное предназначение ADAL — упростить процесс получения маркеров доступа. Чтобы показать, насколько это просто, создадим приложение To Do List (список дел) для Android, которое:

-	Получает маркеры доступа для вызова интерфейса API приложения To Do List с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Получает список дел пользователя
-	Обеспечивает функцию выхода пользователя из приложения.

Чтобы приступить к работе, потребуется клиент (тенант) Azure AD, в котором можно создавать пользователей и регистрировать приложение. Если у вас еще нет клиента, [узнайте, как его получить](active-directory-howto-tenant.md).

## Этап 1. Загрузка и запуск образца службы, реализующей интерфейс REST API приложения To Do List для Node.js

Описываемый в этой статье образец специально создается так, чтобы можно было сравнить его работу с существующим образцом, создающим интерфейс REST API для однотенантного приложения To Do List под управлением Azure AD. Вышеупомянутый образец службы необходим для данного примера быстрого старта.

Для получения информации о его настройке просмотрите существующие примеры здесь:

* [Образец службы REST API под управлением Azure AD для Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## Этап 2. Регистрация веб-API с помощью клиента Microsoft Azure AD

**Что я делаю?**

*Служба каталогов Microsoft Active Directory позволяет добавлять приложения двух типов: веб-интерфейсы API, которые предоставляют услуги пользователям, и приложения (работающие через Интернет или на устройстве), использующие эти веб-API. На этом этапе вы зарегистрируете веб-API, который будет работать локально для тестирования данного образца. Обычно этот веб-API является службой REST, предоставляющей приложению необходимую функциональность. Microsoft Azure Active Directory может обеспечить защитой любую конечную точку!*

*Здесь предполагается, что вы регистрируете интерфейс REST API приложения To Do List, но описанная процедура регистрации работает для любого веб-API, защищаемого службой Azure Active Directory.*

Действия по регистрации веб-API с помощью Microsoft Azure AD

1. Войдите на [Портал управления Azure](https://manage.windowsazure.com).
2. Щелкните "Active Directory" в левой панели навигации.
3. Выберите клиента каталога, в котором нужно зарегистрировать приложение-пример.
4. Откройте вкладку "Приложения".
5. На выдвижной панели нажмите Добавить.
6. Щелкните "Добавить приложение, разрабатываемое моей организацией".
7. Введите понятное имя для приложения, например "TodoListService", выберите "Веб-приложение и/или веб-API" и нажмите кнопку "Далее".
8. В качестве URL-адреса единого входа введите базовый URL-адрес образца, который по умолчанию имеет значение `https://localhost:8080`.
9. В качестве идентификатора URI приложения введите `https://<your_tenant_name>/TodoListService`, заменив `<your_tenant_name>` на имя вашего клиента Azure AD. Для завершения регистрации нажмите кнопку "OK".
10. Не выходя из портала Azure, откройте вкладку "Конфигурация" приложения.
11. **Найдите значение идентификатора клиента и скопируйте его**, оно потребуются позже при настройке приложения.

## Этап 3. Регистрация образца нативного клиентского приложения для Android

Прежде всего необходимо зарегистрировать веб-приложение. Далее необходимо сообщить о приложении в Azure AD. Это позволяет приложению взаимодействовать с только что зарегистрированным веб-API.

**Что я делаю?**

*Как уже говорилось, служба каталогов Microsoft Active Directory позволяет добавлять приложения двух типов: веб-интерфейсы API, которые предоставляют услуги пользователям, и приложения (работающие через Интернет или на устройстве), использующие эти веб-API. На этом этапе регистрируется приложение-образец. Это необходимо для того, чтобы приложение могло запросить доступ к только что зарегистрированному веб-API. Если приложение не зарегистрировано, то Azure AD не позволит ему даже запросить данные для входа. Это часть модели безопасности.*

*Здесь выполняется регистрация приложения-образца, но описанная процедура регистрации будет работать для любого разрабатываемого приложения.*

**Почему приложение и веб-интерфейс API размещаются в одном клиенте?**

*Как вы могли догадаться, можно создать приложение, которое обращается к внешнему интерфейсу API, зарегистрированному в Azure AD из другого клиента. При этом пользователям будет предложено дать согласие на использование этого интерфейса API в приложении. Радует то, что библиотека проверки подлинности ADAL для iOS выполняет все необходимые операции для обработки данного согласия! В более сложных задачах становится очевидным, что обработка согласия является важной составляющей доступа к интерфейсам Microsoft API от Azure и Office, а также любого другого поставщика услуг. Пока приложение и веб-интерфейс зарегистрированы в рамках одного и того же клиента, пользователь не увидит запросы на согласие. Это типично для приложений, предназначенных для использования только внутри компании.*

1. Войдите на [Портал управления Azure](https://manage.windowsazure.com).
2. Щелкните "Active Directory" в левой панели навигации.
3. Выберите клиента каталога, в котором нужно зарегистрировать приложение-пример.
4. Откройте вкладку "Приложения".
5. На выдвижной панели нажмите Добавить.
6. Щелкните "Добавить приложение, разрабатываемое моей организацией".
7. Введите понятное имя для приложения, например "TodoListService", выберите "Собственное клиентское приложение" и нажмите кнопку "Далее".
8. В качестве URI переадресации введите `http://TodoListClient`. Нажмите кнопку "Готово"
9. Перейдите на вкладку приложения "Настройка".
10. Найдите значение идентификатора клиента и скопируйте его, оно потребуются позже при настройке приложения.
11. В меню "Разрешения для других приложений" щелкните "Добавить приложение". Из раскрывающегося списка "Показать" выберите "Другое" и щелкните флажок вверху. Найдите и щелкните TodoListService, затем установите флажок внизу, чтобы добавить приложение. Выберите "Доступ к TodoListService" из меню "Делегированные разрешения" и сохраните конфигурацию.



Для сборки с помощью Maven можно использовать файл pom.xml на верхнем уровне.

  * Клонируйте этот репозиторий в любой каталог по своему усмотрению:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`

  * Выполните действия, описанные в разделе [Предварительные требования настройки Maven для Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  * Установите эмулятор с SDK 19.
  * Перейдите в корневую папку, куда был клонирован репозиторий.
  * Выполните команду: mvn clean install
  * Перейдите в каталог примера быстрого старта: cd samples\\hello
  * Выполните команду: mvn android:deploy android:run
  * Приложение будет запущено.
  * Введите учетные данные тестового пользователя.

JAR-пакеты будут также отправляться наряду с AAR-пакетом.

### Этап 4. Загрузка библиотеки ADAL для Android и добавление ее в рабочую область платформы Eclipse

Существует несколько способов использования данной библиотеки в проекте для Android:

* Можно импортировать библиотеку в платформу Eclipse в виде исходного кода и связать с приложением.
* В Android Studio можно использовать *AAR*-пакет и сослаться на двоичные файлы.

####Способ 1: ZIP-архив с файлами исходного кода

Чтобы загрузить копию исходного кода, в правой части страницы щелкните "Download ZIP" ("Загрузить ZIP") или щелкните [здесь](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####Способ 2: исходные файлы через Git

Чтобы получить исходный код пакета SDK через git, просто введите:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####Способ 3: двоичные файлы через Gradle

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

####Способ 4: AAR через Maven

Если используется подключаемый модуль m2e в Eclipse, можно задать зависимость в файле pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####Способ 5: JAR-пакет в папке libs
Можно перетащить JAR-файл из репозитория maven в папку *libs* проекта. Вы должны скопировать требуемые ресурсы в свой проект, поскольку они не включены в JAR-пакеты.


### Этап 5: добавление ссылки на библиотеку ADAL для Android в проект


2. Добавьте в проект ссылку на библиотеку для Android. Если вы затрудняетесь это сделать, [щелкните здесь для получения дополнительных сведений](http://developer.android.com/tools/projects/projects-eclipse.html).

3. Добавьте в параметры проекта зависимость проекта для отладки.

4. Добавьте в файл проекта AndroidManifest.xml следующее:

    ```Java
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
    ```

7. Создайте экземпляр класса AuthenticationContext с вашим MainActivity. Описание этого вызова выходят за рамки данной статьи, но вы можете получить дополнительные сведения в разделе [Образец клиентского нативного приложения для Android](https://github.com/AzureADSamples/NativeClient-Android). Ниже приведен пример:

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * Примечание: mContext — это поле в вашем классе Activity.

8. Скопируйте этот блок кода для обработки в конец AuthenticationActivity после фрагмента, где пользователь вводит учетные данные и получает код авторизации:

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Чтобы запросить маркер, определите функцию обратного вызова:

    ```Java
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
    ```
10. Наконец, запросите маркер с помощью данного обратного вызова:

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Объяснение параметров:

  * Параметр resource является обязательным и указывает ресурс, к которому требуется получить доступ.
  * Параметр clientId является обязательным и поступает из портала Azure AD.
  * В качестве параметра redirect (URL-адрес перенаправления) можно задать код URI имени пакета. Он не требуется для вызова acquireToken.
  * Параметр PromptBehavior указывает на необходимость запроса учетных данных, а не получения их из кэша или файла cookie.
  * Функция обратного вызова callback будет выполнена после того, как произойдет обмен кода авторизации на маркер.

  Функция обратного вызова callback получит объект AuthenticationResult, который имеет маркер доступа, дату окончания срока действия и сведения об идентификаторе маркера.

Необязательно: **acquireTokenSilent**.

Можно вызвать метод **acquireTokenSilent** для обработки кэшированных данных и обновления маркеров. Также данный метод обеспечивает возможность выполнить синхронный вызов. Он принимает userid в качестве параметра.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Broker**.
  Компонент broker предоставляется приложением корпоративного портала Microsoft Intune. Библиотека ADAL будет использовать учетную запись broker, если существует одноименная пользовательская учетная запись, созданная в этой структуре проверки подлинности, и разработчик решил не игнорировать ее. Разработчик может игнорировать (пропустить) пользователя broker с помощью следующего вызова:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Для использования broker разработчику необходимо зарегистрировать специальный URI перенаправления. URI перенаправления имеет формат msauth://packagename/Base64UrlencodedSignature. Можно получить URI перенаправления для приложения с помощью скрипта brokerRedirectPrint.ps1 или с помощью вызова метода API mContext.getBrokerRedirectUri. Подпись связана с вашими сертификатами подписи.

 Текущая модель broker предназначена для одного пользователя. Класс AuthenticationContext предоставляет метод API для получения пользователя broker:

 ```java
 String brokerAccount =  mContext.getBrokerUser();
```
Метод возвращает пользователя broker, если учетная запись является действительной.

 Манифест приложения должен иметь следующие разрешения на использование учетных записей в диспетчере учетных записей: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


Данное пошаговое руководство предоставляет все необходимое для успешной интеграции с Azure AD. Дополнительные примеры представлены в репозитории AzureADSamples/ на портале GitHub.

## Важная информация

### Настройка

Ресурсы проекта библиотеки могут быть перезаписаны ресурсами вашего приложения. Перезапись происходит при сборке приложения. Поэтому вы можете настроить макет AuthenticationActivity так, как требуется. Необходимо обязательно сохранять идентификаторы элементов управления, которые используются библиотекой ADAL (веб-представление).

### Broker

Компонент Broker предоставляется приложением корпоративного портала Microsoft Intune. Учетная запись создается в диспетчере учетных записей. Тип учетной записи — "com.microsoft.workaccount". Компонент позволяет создать только одну учетную запись единого входа. Компонент создает файл cookie единого входа для данного пользователя после завершения запроса устройства одним из приложений.

### URL-адрес центра и служба федерации Active Directory

Служба федерации Active Directory не считается службой маркеров безопасности, поэтому необходимо включить обнаружение экземпляра службы и передать значение false в конструктор AuthenticationContext.

URL-адресу центра необходим экземпляр службы маркеров безопасности и имя клиента: https://login.windows.net/yourtenant.onmicrosoft.com

### Запрос элементов кэша

Библиотека ADAL предоставляет кэш по умолчанию в SharedPrefrecens и несколько простых функций для запроса данных из кэша. Вот как можно получить текущий кэш из AuthenticationContext.
```Java
 ITokenCacheStore cache = mContext.getCache();
```
Кроме того, можно предоставить собственную реализацию кэша, если вы хотите его настроить.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### PromptBehavior

ADAL предоставляет возможность указать, требуется ли отображать диалоговое окно. При указании параметра PromptBehavior.Auto будет отображено диалоговое окно, только если маркер обновления недействителен и требуются учетные данные пользователя. При указании параметра PromptBehavior.Always данные кэша игнорируются, и диалоговое окно запроса учетных данных будет отображаться всегда.

### Автоматический запрос маркера из кэша и обновление

Этот метод не использует диалоговое окно запроса учетных данных и не требует от пользователя каких-либо действий. Он возвращает маркер из кэша, если тот доступен. Если срок действия маркера истек, метод попытается обновить его. Если срок действия маркера обновления истек или маркер ошибочен, метод вызовет исключение AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

Также данный метод обеспечивает возможность выполнить синхронный вызов. Для параметра callback используйте acquireTokenSilentSync или задайте значение null.

### Диагностика

Ниже представлены основные источники информации для диагностики проблем:

+ Исключения
+ Журналы
+ Данные трассировки сети

Следует отметить, что в библиотеке наиболее важными для диагностики являются идентификаторы корреляции. Вы можете установить собственные идентификаторы корреляции для отдельных запросов, если требуется сопоставить запрос ADAL с другими операциями в коде. Если идентификатор корреляции не задан, то библиотека ADAL создает случайный идентификатор, и все сообщения журнала и сетевые вызовы будут помечены этим идентификатором корреляции. Генерируемый идентификатор изменяется при каждом запросе.

#### Исключения

Очевидно, это первый этап диагностики. Мы стараемся предоставить содержательные сообщения об ошибках. Если вы не считаете какое-либо сообщение содержательным, пожалуйста, сообщите нам об этом. Также, пожалуйста, укажите такие сведения об устройстве, как модель и версия SDK.

#### Журналы

Можно настроить библиотеку на создание сообщений журнала, которые будут использоваться для диагностики проблем. Чтобы настроить ведение журнала, используйте следующую функцию обратного вызова, которая будет вызываться библиотекой ADAL для передачи сообщений журнала при их создании.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
```
Сообщения могут записываться в файл пользовательского журнала, как показано ниже. К сожалению, нет стандартного способа получения журналов с устройств. Существуют некоторые службы, которые могут в этом помочь. Также вы можете создать свою службу, например для отправки файла на сервер.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### Уровни протоколирования

+ Ошибка (исключение)
+ Оповещение (предупреждение)
+ Информация (информирование)
+ Подробная информация (дополнительные сведения)

Настройка уровня ведения журнала происходит следующим образом.
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Все сообщения журнала передаются в logcat в дополнение ко всем обратным вызовам пользовательского журнала. Ниже показано, как получить файл журнала из logcat:

 ```
  adb logcat > "C:\logmsg\logfile.txt"
```
 Дополнительные примеры команд adb: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### Данные трассировки сети

Для перехвата HTTP-трафика, который создает библиотека ADAL, можно использовать различные инструменты. Это очень полезно, если вы знакомы с протоколом OAuth или если необходимо предоставить диагностические сведения в корпорацию Майкрософт или другие каналы поддержки.

Fiddler является простым инструментом трассировки HTTP. Используйте следующие ссылки для получения информации о правильной настройке инструмента Fiddler для записи сетевого трафика библиотеки ADAL. Чтобы достичь положительного результата, Fiddler или любой другой инструмент, такой как Charles, должен быть настроен для записи незашифрованного трафика SSL. ПРИМЕЧАНИЕ: данные трассировки, записанные таким образом, могут содержать конфиденциальные сведения, такие как маркеры доступа, имена пользователей и пароли. При использовании рабочих учетных записей не передавайте данные трассировки 3-им сторонам. Если для получения поддержки необходимо предоставить данные трассировки другому пользователю, воспроизведите проблему с временной учетной записью, используя имена пользователей и пароли, которые не будут использоваться для реальных пользователей.

+ [Настройка Fiddler для Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Настройка правил Fiddler для библиотеки ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### Режим диалогового окна
Метод acquireToken с нулевым первым параметром приведет к отображению диалогового окна.

### Шифрование

ADAL шифрует маркеры и по умолчанию хранит их в SharedPreferences. Вы можете подробно рассмотреть класс StorageHelper. В Android 4.3 (API уровня 18) введено безопасное хранилище закрытых ключей AndroidKeyStore. Библиотека ADAL использует это хранилище при работе с API уровня 18 и выше. Если требуется использовать ADAL с более ранними версиями SDK, необходимо предоставить секретный ключ в AuthenticationSettings.INSTANCE.setSecretKey.

### Запрос носителя OAuth2

Класс AuthenticationParameters предоставляет функциональные возможности для получения authorization\_uri из запроса носителя OAuth2.

### Файлы cookie сеанса в веб-представлении

После закрытия приложения веб-представление Android не очищает файлы cookie сеанса. Выполнить очистку можно с помощью следующего примера кода.
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Подробнее о файлах cookie: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### Переопределения ресурсов

Библиотека ADAL содержит указанные ниже строки на английском языке для элементов пользовательского интерфейса двух диалоговых окон.

Разработчику следует перезаписать эти сроки, если требуется их локализовать.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### Диалоговое окно NTLM
Версия ADAL 1.1.0 поддерживает диалоговое окно NTLM, которое отображается по событию onReceivedHttpAuthRequest, поступающему из WebViewClient. Макет и строки диалогового окна можно настроить.

### Единый вход для нескольких приложений
См. раздел [How to enable cross-app SSO on Android using ADAL](active-directory-sso-android.md).


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->