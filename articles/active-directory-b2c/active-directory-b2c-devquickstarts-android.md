<properties pageTitle="Предварительная версия Azure AD B2C: вызов веб-API из веб-приложения Android | Microsoft Azure" description="В этой статье показано, как создать приложение Android "Список дел", вызывающее веб-API node.js с помощью маркеров носителей OAuth 2.0. Как приложение Android, так и веб-API используют Azure AD B2C для управления удостоверениями пользователей и проверки подлинности пользователей." services="active-directory-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="brandwe"/>

# Предварительна версия Azure AD B2C: вызов веб-API из приложения Android

Azure AD B2C позволяет добавить в приложение Android или веб-приложение мощные функции самостоятельного управления удостоверениями, выполнив несколько простых действий. В этой статье показано, как создать приложение Android "Список дел", вызывающее веб-API node.js с помощью маркеров носителей OAuth 2.0. Как приложение Android, так и веб-API используют Azure AD B2C для управления удостоверениями пользователей и проверки подлинности пользователей.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]
	Для работы с этим кратким руководством требуется веб-API, защищенный Azure AD с B2C. Мы создали для вас файлы .NET и node.js. 
	В данном пошаговом руководстве предполагается, что образец веб-API в файле node.js уже настроен. См. [руководство по веб-API Azure AD B2C для Node.js](active-directory-b2c-devquickstarts-api-node.md).

> [AZURE.NOTE]
	В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности. 
    Прочитайте [руководство по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md), чтобы изучить основы Azure AD B2C (если вы еще этого не сделали).

Клиентские приложения для Android, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL). Единственное предназначение ADAL — упростить процесс получения маркеров доступа. Чтобы показать, насколько это просто, создадим приложение To Do List (список дел) для Android, которое:

-	Получает маркеры доступа для вызова интерфейса API приложения To Do List с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Получает список дел пользователя
-	выполняет выход пользователя из приложения.



### Этап 1. Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md).

### Этап 2. Создание приложения

Теперь необходимо создать приложение в каталоге B2C. Оно будет передавать в Azure AD сведения, необходимые для безопасного взаимодействия с вашим приложением. В этом случае приложение и веб-API будут представлены одним **идентификатором приложения**, поскольку они включают в себя одно приложение логики. Создайте приложение, выполнив [эти инструкции](active-directory-b2c-app-registration.md). Не забудьте

- Включить в приложение **веб-приложение или веб-API**.
- Введите `http://localhost:3000/auth/openid/return` как **URL-адрес ответа**. Это URL-адрес по умолчанию для этого образца кода.
- Создайте для своего приложения **секрет приложения** и скопируйте его. Скоро он вам понадобится.
- Скопируйте **идентификатор приложения**, назначенный приложению. Он также вам скоро понадобится.

### Этап 3. Создание политик

В Azure AD B2C взаимодействие с каждым пользователем определяется [**политикой**](active-directory-b2c-reference-policies.md). Это приложение содержит три вида идентификации: регистрация, вход и вход с помощью учетной записи Facebook. Вам нужно создать по одной политике каждого типа, как описано в [справочнике по политикам](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех политик обязательно сделайте следующее.

- Выберите в политике регистрации значение для атрибута **Отображаемое имя** и других атрибутов регистрации.
- Выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта** в каждой политике. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. Оно должно включать префикс `b2c_1_`. Эти имена политик понадобятся вам через некоторое время. 

Создав три политики, можно приступать к созданию приложения.

Обратите внимание, что в данной статье не рассматривается использование политик, которые вы только что создали. Дополнительные сведения о работе политик в Azure AD B2C см. в [руководстве по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

### Этап 4. Скачивание кода

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Чтобы построить образец, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip) или клонируйте ее.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**Для изучения этого учебника необходимо загрузить схему.** В связи со сложностью реализации полнофункционального приложения Android **основа** должна содержать UX-код, который будет выполнен после завершения данного руководства. Это позволит разработчику сэкономить время. UX-код не связан с темой добавления B2C в приложение Android.

Готовое приложение также [доступно в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) или ветви `complete` того же репозитория.


Для сборки с помощью Maven можно использовать файл pom.xml на верхнем уровне.


  * Выполните действия, описанные в разделе [Предварительные требования настройки Maven для Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  * Установите эмулятор с SDK 21.
  * Перейдите в корневую папку, куда был клонирован репозиторий.
  * Выполните команду: mvn clean install
  * Перейдите в каталог примера быстрого старта: cd samples\\hello
  * Выполните команду: mvn android:deploy android:run
  * Приложение будет запущено.
  * Введите учетные данные тестового пользователя.

JAR-пакеты будут также отправляться наряду с AAR-пакетом.

### Этап 5. Загрузка библиотеки ADAL для Android и добавление ее в рабочую область платформы Eclipse

Существует несколько способов использования данной библиотеки в проекте для Android:

* Можно импортировать библиотеку в платформу Eclipse в виде исходного кода и связать с приложением.
* В Android Studio можно использовать *AAR*-пакет и сослаться на двоичные файлы.

####Способ 1. Исходные файлы через Git

Чтобы получить исходный код пакета SDK через git, просто введите:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"

####Способ 2. Двоичные файлы через Gradle

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
    compile('com.microsoft.aad:adal:2.0-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0-alpha
}
```

####Способ 3. AAR через Maven

Если используется подключаемый модуль m2e в Eclipse, можно задать зависимость в файле pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0-alpha</version>
    <type>aar</type>
</dependency>
```


####Способ 5: JAR-пакет в папке libs
Можно перетащить JAR-файл из репозитория maven в папку *libs* проекта. Вы должны скопировать требуемые ресурсы в свой проект, поскольку они не включены в JAR-пакеты.


### Этап 6. Добавление ссылки на библиотеку ADAL для Android в проект


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

### Шаг 7. Добавление кода вызова ADAL для Android

Создадим основное действие и назовем его `ToDoActivity`.

Затем инициализируем это действие и добавим несколько кнопок для управления пользовательским интерфейсом. Если вы уже писали код для Android, эта простая процедура должна быть вам знакома.

```
public class ToDoActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();
```

Затем вставьте добавленные кнопки для управления пользовательским интерфейсом в тот же класс (не закрывая скобку). Мы добавим кнопки для входа с помощью учетной записи Facebook и по адресу электронной почты. Для этого будут использоваться политики, определенные в файле `constants.java`.

```
        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

    ```
Create an instance of AuthenticationContext at your main Activity:

    ```Java
 		 mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false, InMemoryCacheStore.getInstance());
            mAuthContext.getCache().removeAll();

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
    ```
  * NOTE: mContext is a field in your activity
 
 Let's also define our Result method `onActivityResult` that will be called when we have a result from the callback we'll write later:
 
 ```
 @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
 ```

Next, let's define our callback:

    ```Java
    mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY, Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getAccessToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
        }
        Toast.makeText(getApplicationContext(), TAG + "done", Toast.LENGTH_SHORT).show();
    }
        ```
        
 You may notice that this relies on methods we haven't written yet, such as `updateLoggedInUser()` and `getTasks()`. We'll write those below.	You can safely ignore the errors in Android Studio for now.

Explanation of the parameters:

  * ***SCOPES*** is required and is the scopes you are trying to reqeust access for. For the B2C preview this is the same as the Clientid but will change in the future.
  * ***POLICY*** is the policy for while you wish to authenticate the user. 
  * ***CLIENT_ID*** is required and comes from the AzureAD Portal.
  * You can setup redirectUri as your packagename. It is not required to be provided for the acquireToken call.
  * ***USER_HINT*** is the way we look up if the user is already in the cache and prompt the user if they are not found or the access token is invalid.
  * ***PROMPT*** helps to ask for credentials to skip cache and cookie.
  * ***Callback*** will be called after authorization code is exchanged for a token.

  The Callback will have an object of AuthenticationResult which has accesstoken, date expired, and idtoken info.

> [AZURE.NOTE]	Microsoft Intune's Company portal app provides the broker component and may be installed on the user's device. Developers should be prepared to allow for it's use as it provides SSO across all applications on the device. ADAL for Android will use the broker account if there is one user account created in the Authenticator. To use the broker, the developer needs to register a special redirectUri for broker usage. RedirectUri is in the format of msauth://packagename/Base64UrlencodedSignature. You can get your redirecturi for your app using the script `brokerRedirectPrint.ps1` or use API call `mContext.getBrokerRedirectUri()`. The signature is related to your signing certificates from the Google Play store.

 A Developer can skip the broker user with:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.WARNING] In order to reduce the complexity of this B2C Quickstart, we have opted in our sample to skip the broker. However, this is not recommended.

Next, let's create some helper methods that will get the token alone during our authentication calls to the Task API:

```
private void getToken(final AuthenticationCallback callback) {

        // one of the acquireToken overloads
        mAuthContext.acquireToken(onnstants.SCOPES, Constants.ADDITIONAL_SCOPES, Constants.POLICY,
                Constants.CLIENT_ID, Constants.REDIRECT_URL, Constants.USER_HINT, Constants.PROMPT,
                "nux=1&" + Constants.EXTRA_QP, callback);
    }

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {
        Constants.CURRENT_RESULT = newToken;
    }
    
```

Finally, Your app manifest should have permissions to use AccountManager accounts: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


### Step 8. Call the Task API

Now that we have our Activity wired up and ready to go to do the heavy lifting of grabbing tokens, let's write our API to access the task server.

Our `getTasks` provides an array that represents the tasks in our server 
Our `addTask` and `deleteTask` do the subsequent action and return TRUE or FALSE if it was successful.

Let's write our `getTask` first:

```
private void getTasks() { if (Constants.CURRENT\_RESULT == null || Constants.CURRENT\_RESULT.getAccessToken().isEmpty()) return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(Constants.CURRENT_RESULT.getAccessToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }
 ```
 
 Напишем также метод, инициализирующий таблицы при первом запуске.
 
 ```
     private void initAppTables() {
        try {
            // Get the Mobile Service Table instance to use
//            mToDoTable = mClient.getTable(WorkItem.class);
//            mToDoTable.TABvLES_URL = "/api/";
            //mTextNewToDo = (EditText)findViewById(R.id.listViewToDo);


            // Create an adapter to bind the items with the view
            //mAdapter = new WorkItemAdapter(ToDoActivity.this, R.layout.listViewToDo);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 You'll see that this code requires some additional methods to do it's work. Let's write those now.
 
 ### Create endpoint URL generator
 
 We need to generate the endpoint URL that we'll be connecting to. Let's do that in the same class file:
 
 
 ```
     private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

    ```


Note that we add the access token to the request in the following code:

### Step 9: Let's write some UX methods

Android requires us to handle some callbacks in order to operate the app. These are `createAndShowDialog` and `onResume()`. This is pretty simple and very familiar if you've written Android code before. 

Let's write those now:

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }
    
```

And now manage our dialog callbacks:


```
/ ** * Создает диалоговое окно и выводит его на экран * * @param exception Исключение для отображения в диалоговом окне * @param title Заголовок диалогового окна */ private void createAndShowDialog(Exception exception, String title) { createAndShowDialog(exception.toString(), title); }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }
    
 ```
    


### Этап 10. Запуск примера приложения

Наконец, создадим и запустим приложение в Android Studio или Eclipse. Зарегистрируйтесь или войдите в приложение и создайте задачи для пользователя, вошедшего в систему. Выйдите и снова войдите от имени другого пользователя. Создайте задачи для этого пользователя.

Обратите внимание, что в API хранятся задачи для каждого пользователя, поскольку API извлекает удостоверение пользователя из получаемого маркера доступа.

Готовый пример для сравнения в виде ZIP-файла находится [здесь](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Также можно клонировать его с GitHub.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


<!--

### Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

### Important Information


#### Encryption

ADAL encrypts the tokens and store in SharedPreferences by default. You can look at the StorageHelper class to see the details. Android introduced AndroidKeyStore for 4.3(API18) secure storage of private keys. ADAL uses that for API18 and above. If you want to use ADAL for lower SDK versions, you need to provide secret key at AuthenticationSettings.INSTANCE.setSecretKey

#### Session cookies in Webview

Android webview does not clear session cookies after app is closed. You can handle this with sample code below:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Подробнее о файлах cookie: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=Sept15_HO3-->