<properties pageTitle="Предварительная версия Azure AD B2C: вызов веб-API из веб-приложения Android | Microsoft Azure" description="В этой статье показано, как создать приложение Android "Список дел", вызывающее веб-API node.js с помощью маркеров носителей OAuth 2.0. Как приложение Android, так и веб-API используют Azure AD B2C для управления удостоверениями пользователей и проверки подлинности пользователей." services="active-directory-b2c" documentationCenter="android" authors="brandwe" manager="msmbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# Предварительна версия Azure AD B2C: вызов веб-API из приложения Android

Azure AD B2C позволяет добавить в приложения Android или веб-API мощные функции самостоятельного управления удостоверениями, выполнив несколько простых действий. В этой статье показано, как создать приложение Android "Список дел", вызывающее веб-API node.js с помощью маркеров носителей OAuth 2.0. Как приложение Android, так и веб-API используют Azure AD B2C для управления удостоверениями пользователей и проверки подлинности пользователей.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

 	
> [AZURE.NOTE]Для работы с этим кратким руководством требуется веб-API, защищенный Azure AD с B2C. Мы создали для вас файлы .NET и node.js. В данном пошаговом руководстве предполагается, что образец веб-API в файле node.js уже настроен. См. [руководство по веб-API Azure AD B2C для Node.js](active-directory-b2c-devquickstarts-api-node.md).

 
> [AZURE.NOTE]В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности. Прочитайте [руководство по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md), чтобы изучить основы Azure AD B2C (если вы еще этого не сделали).


Клиентские приложения для Android, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL). Единственное предназначение ADAL — упростить процесс получения маркеров доступа. Чтобы показать, насколько это просто, создадим приложение To Do List (список дел) для Android, которое:

-	Получает маркеры доступа для вызова интерфейса API приложения To Do List с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Получает список дел пользователя
-	выполняет выход пользователя из приложения.



### Этап 1. Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md).

### Этап 2. Создание приложения

Теперь необходимо создать приложение в каталоге B2C. Оно будет передавать в Azure AD сведения, необходимые для безопасного взаимодействия с вашим приложением. В этом случае приложение и веб-API будут представлены одним **идентификатором приложения**, поскольку они включают в себя одно приложение логики. Создайте приложение, выполнив [эти инструкции](active-directory-b2c-app-registration.md). Не забудьте

- Включить в приложение **веб-приложение или веб-API**.
- Введите `urn:ietf:wg:oauth:2.0:oob` как **URL-адрес ответа**. Это — URL-адрес по умолчанию для этого образца кода.
- Создайте для своего приложения **секрет приложения** и скопируйте его. Скоро он вам понадобится.
- Скопируйте **идентификатор приложения**, назначенный приложению. Он также вам скоро понадобится.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

### Этап 3. Создание политик

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

В Azure AD B2C всякое взаимодействие с пользователем определяется [**политикой**](active-directory-b2c-reference-policies.md). Это приложение содержит три вида идентификации: регистрация, вход и вход с помощью учетной записи Facebook. Вам потребуется создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех политик обязательно сделайте следующее.

- В политике регистрации выберите **Отображаемое имя** и другие атрибуты регистрации.
- В каждой политике выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. У него должен быть префикс `b2c_1_`. Эти имена политик понадобятся вам через некоторое время. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к созданию приложения.

Обратите внимание, что в данной статье не рассматривается использование политик, которые вы только что создали. Сведения о работе политик в Azure AD B2C см. в [руководстве по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

### Этап 4. Скачивание кода

Код для этого учебника размещен на веб-сайте [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Чтобы выполнить сборку примера, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip) или клонируйте ее.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE]**Для изучения этого учебника необходимо загрузить схему.** Так как полнофункциональное приложение для Android сложно реализовать, **схема** содержит код взаимодействия с пользователем, который будет выполнен после завершения работы с данным учебником. Это позволит разработчику сэкономить время. UX-код не связан с темой добавления B2C в приложение Android.

Кроме того, готовое приложение можно [скачать как ZIP-архив](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) или получить из ветви `complete` того же репозитория.


Для сборки с помощью Maven можно использовать файл pom.xml на верхнем уровне.


  * Выполните действия, описанные в разделе [предварительных требований по настройке Maven для Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  * Установите эмулятор с SDK 21.
  * Перейдите в корневую папку, куда был клонирован репозиторий.
  * Выполните команду: mvn clean install
  * Перейдите в каталог примера быстрого старта: cd samples\\hello
  * Выполните команду: mvn android:deploy android:run
  * Приложение будет запущено.
  * Введите учетные данные тестового пользователя.

JAR-пакеты будут также отправляться наряду с AAR-пакетом.

### Этап 5. Загрузка библиотеки ADAL для Android и добавление ее в рабочую область платформы Android Studio

Существует несколько способов использования данной библиотеки в проекте для Android:

* Можно импортировать библиотеку в платформу Eclipse в виде исходного кода и связать с приложением.
* В Android Studio можно использовать *AAR*-пакет и сослаться на двоичные файлы.



####Вариант 1. Двоичные файлы через Gradle (рекомендуется)

Двоичные файлы можно получить из центрального репозитория Maven. Пакет AAR можно включить в проект Android Studio указанным ниже образом (пример в `build.gradle`).

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

####Способ 2. AAR через Maven

Если вы используете подключаемый модуль m2e в Eclipse, то можно задать зависимость в файле `pom.xml`:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0-alpha</version>
    <type>aar</type>
</dependency>
```

####Вариант 3. Исходные файлы через Git (последнее средство)

Чтобы получить исходный код пакета SDK через git, просто введите:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src
    
    use the branch "convergence"


### Шаг 6. Настройка файла конфигурации

Для настройки проекта Android мы будем использовать конфигурацию, настроенную ранее на портале B2C.

Откройте `helpes/Constants.java` и присвойте необходимые значения указанным ниже параметрам.

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
**SCOPES** — это области, передаваемые на сервер, который будет запрашиваться с сервера для входа пользователя. Для предварительной версии B2C мы передаем идентификатор клиента. Однако в будущем вместо это будет использоваться чтение областей. После этого документ будет обновлен. **ADDITIONAL\_SCOPES** — это дополнительные области, которые могут потребоваться приложению. Этот параметр будет использоваться в будущем. **CLIENT\_ID** — идентификатор приложения, полученный с портала. **REDIRECT\_URL** — это URL-адрес переадресации для обратной публикации маркера. **EXTRA\_QP** — любые дополнительные сведения, которые необходимо передать на сервер в формате URL-адреса. **FB\_POLICY** — вызываемая политика. Это самая важная часть данного руководства. **EMAIL\_SIGNIN\_POLICY** — вызываемая политика. Это самая важная часть данного руководства. **EMAIL\_SIGNUP\_POLICY** — вызываемая политика. Это самая важная часть данного руководства.

### Этап 7. Добавление ссылки на библиотеку ADAL для Android в проект


> [AZURE.NOTE]В ADAL для Android для вызова проверки подлинности используется модель на основе намерений. Намерения работают, "накладываясь" на приложение. Весь этот пример (как и использование ADAL для Android в целом) связан с управлением намерениями и передачей информации между ними.


Прежде всего, нам необходимо передать в Android макет приложения, содержащий необходимые намерения. Подробнее мы обсудим намерения позднее.

Добавьте в файл проекта AndroidManifest.xml следующие намерения:

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Как видите, здесь определены 5 действий для дальнейшего использования.

Параметр **AuthenticationActivity** поступает из ADAL и обеспечивает веб-представление для входа.

Параметр **LoginActivity** отображает наши политики входа и кнопки для каждой политики.

Параметр **SettingsActivity** позволяет изменить параметры приложения в среде выполнения.

Параметр **AddTaskActivity** позволяет добавлять в API REST задачи, защищенные Azure AD.

**ToDoActivity** — основное действие, отображающее задачи.



### Шаг 8. Создание действия входа

Создадим основное действие и назовем его `LoginActivity`.

Создайте файл `LoginActivity.java`.

Затем инициализируем это действие и добавим несколько кнопок для управления пользовательским интерфейсом. Если вы уже писали код для Android, эта простая процедура должна быть вам знакома.

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

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

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
Мы создали кнопки, вызывающие наше намерение ToDoActivity (оно будет вызывать ADAL, если нам потребуется маркер), используя наше собственное действие в качестве ссылки и дополнительного параметра. Этот дополнительный параметр передается при помощи метода `intent.putExtra()`. Здесь мы определяем параметр thePolicy, который вы указали в `Constants.java`. Таким образом, намерение узнает, какую политику нужно вызывать в процессе проверки подлинности.

### Шаг 9. Создание действия SettingsActivity

Это — действие, заполняющее пользовательский интерфейс параметров.

Создайте файл `SettingsActivity.java`.

CRUD здесь выглядит довольно просто:

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

	//private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_settings);

        loadSettings();
//		checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//		checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


	}

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

	private void saveSettings(String key, boolean value) {
		SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
				Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
		Editor prefsEditor = prefs.edit();
		prefsEditor.putBoolean(key, value);
		prefsEditor.commit();
	}
}
```

### Шаг 10. Создание действия AddTask

Это действие позволяет добавить задачу в конечную точку API REST. Здесь тоже все довольно просто.

Создайте файл `AddTaskActivity.java`.

Теперь запишите код:

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

### Шаг 11. Создание действия ToDoList

Теперь у нас есть главное действие, позволяющее получить маркер для политики из Azure AD и использовать его для вызова API REST сервера задачи.

Создайте файл `ToDoActivity.java`.

Запишите следующий код (вызовы методов мы рассмотрим ниже):

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

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
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
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

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}
   
```

        
 Вероятно, вы заметили, что здесь используются методы, которые мы еще не написали, в частности `updateLoggedInUser()`, `clearSessionCookie()` и `getTasks()`. Мы напишем их ниже. На данном этапе ошибки в Android Studio можно игнорировать.

Объяснение параметров:

  * ***SCOPES*** — это обязательный параметр. Он представляет области, доступ к которым вы пытаетесь запросить. В предварительной версии B2C этот параметр представляет собой то же самое, что и Clientid, но в будущем это изменится.
  * ***POLICY*** — это политика для проверки подлинности пользователя. 
  * ***CLIENT\_ID*** — это обязательный параметр, который передается с портала Azure AD.
  * В качестве параметра redirect (URL-адрес перенаправления) можно задать код URI имени пакета. Он не требуется для вызова acquireToken.
  * ***getUserInfo()*** — это метод, с помощью которого мы определяем, добавлен ли пользователь в кэш, и сообщаем пользователям, если это не так или если используется недопустимый маркер доступа. Мы напишем этот метод ниже.
  * Параметр ***PromptBehavior.always*** указывает на необходимость запроса учетных данных, а не получения их из кэша или файла cookie.
  * Параметр ***Callback*** вызывается после обмена кода авторизации на маркер.

  Функция обратного вызова callback получит объект AuthenticationResult, который имеет маркер доступа, дату окончания срока действия и сведения об идентификаторе маркера.

> [AZURE.NOTE]Приложение Microsoft Intune «Корпоративный портал» выступает в качестве брокера и может устанавливаться на устройства пользователей. Разработчики должны разрешить его использование, поскольку оно позволяет обеспечить единый вход во все приложения на устройстве. При наличии одной учетной записи, созданной в приложении Authenticator, ADAL для Android будет использовать учетную запись брокера. Чтобы использовать брокер, разработчику необходимо зарегистрировать специальный URI перенаправления. URI перенаправления имеет формат msauth://packagename/Base64UrlencodedSignature. Можно получить URI перенаправления для приложения при помощи сценария `brokerRedirectPrint.ps1` или использовать вызов API `mContext.getBrokerRedirectUri()`. Подпись связана с сертификатами подписи из магазина Google Play.

 Разработчики могут пропустить пользователя брокера с помощью следующего вызова:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE]Чтобы упростить быстрое руководство B2C, мы решили пропустить пользователя broker в образце кода.

Теперь создадим вспомогательные методы, которые в результате вызовов проверки подлинности в API Task будут получать только маркер.

**В тот же файл** с именем `ToDoActivity.java` добавим указанный ниже код.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Добавим также ряд методов, которые будут устанавливать и получать значение AuthenticationResult (который содержит наш маркер) в глобальных КОНСТАНТАХ. Это необходимо, так как, хотя в потоках `ToDoActivity.java` и используется **sResult**, при выполнении других наших действий доступ к маркеру будет отсутствовать, следовательно, не будет результата (например, не будет добавлена задача в `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }

    
```
### Шаг 12. Создание метода для возврата объекта UserIdentifier

ADAL для Android представляет пользователя в виде объекта **UserIdentifier**. Он управляет пользователем и позволяет нам определять, используется ли в вызове тот же пользователь и нужно ли нам в связи с этим полагаться на кэш или отправлять новый вызов на сервер. Чтобы упростить эту задачу, создадим метод `getUserInfo()`, возвращающий объект UserIdentifier, который можно использовать для `acquireToken()`. Кроме того, мы создадим метод getUniqueId(), который быстро вернет нам идентификатор из объекта UserIdentifier в нашем кэше.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }
    
```
 
### Шаг 13. Написание некоторых вспомогательных методов

Нам нужно записать ряд вспомогательных методов, которые помогут нам удалить файлы cookie и предоставить AuthenticationCallback. Они используются в нашем примере только для того, чтобы очистить платформу для вызова действия ToDo.

**В тот же файл** с именем `ToDoActivity.java` добавим указанный ниже код.

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
``` 

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }
    
```   

### Шаг 14. Вызов API задачи

Теперь, когда действие настроено и готово взять на себя неподъемный труд по получению маркеров, напишем API для доступа к серверу задач.

Метод `getTasks` возвращает массив, представляющий задачи на сервере.

Сначала напишем метод `getTask`.

**В тот же файл** с именем `ToDoActivity.java` добавим указанный ниже код.

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
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
 
 **В тот же файл** с именем `ToDoActivity.java` добавим указанный ниже код.
 
```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```
 
 Как видите, для работы кода требуются некоторые дополнительные методы. Давайте их напишем.
 
### Создание генератора URL-адресов конечных точек
 
 Нам нужно сгенерировать URL-адрес конечной точки, к которому мы будем подключаться. Сделаем это в файле того же класса.
 
 **В тот же файл** с именем `ToDoActivity.java` добавим указанный ниже код.
 
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


В следующем фрагменте кода мы добавим маркер доступа в запрос.

### Шаг 15. Запись методов UX

Для работы приложения Android требуются обратные вызовы — Это `createAndShowDialog` и `onResume()`. Если вы уже писали код для Android, эта простая процедура должна быть вам знакома.

Давайте запишем код.

**В тот же файл** с именем `ToDoActivity.java` добавим указанный ниже код.

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

Теперь займемся обратными вызовами диалогового окна.

**В тот же файл** с именем `ToDoActivity.java` добавим указанный ниже код.


```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

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

Вот и все! Теперь у вас есть файл `ToDoActivity.java`, который компилируется. На этом этапе должен компилироваться и весь проект.
    


### Этап 16. Запуск примера приложения

Наконец, создадим и запустим приложение в Android Studio или Eclipse. Зарегистрируйтесь или войдите в приложение и создайте задачи для пользователя, вошедшего в систему. Выйдите и снова войдите от имени другого пользователя. Создайте задачи для этого пользователя.

Обратите внимание, что в API хранятся задачи для каждого пользователя, поскольку API извлекает удостоверение пользователя из получаемого маркера доступа.

Готовый пример [в виде ZIP-файла предоставлен здесь](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Кроме того, можно клонировать его с GitHub.

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android
```


### Важная информация


#### Шифрование

ADAL шифрует маркеры и по умолчанию хранит их в SharedPreferences. Вы можете подробно рассмотреть класс StorageHelper. В Android 4.3 (API уровня 18) введено безопасное хранилище закрытых ключей AndroidKeyStore. Библиотека ADAL использует это хранилище при работе с API уровня 18 и выше. Если требуется использовать ADAL с более ранними версиями SDK, необходимо предоставить секретный ключ в AuthenticationSettings.INSTANCE.setSecretKey.

#### Файлы cookie сеанса в веб-представлении

После закрытия приложения веб-представление Android не очищает файлы cookie сеанса. Выполнить очистку можно с помощью следующего примера кода: ```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
``` Подробнее о файлах cookie: http://developer.android.com/reference/android/webkit/CookieSyncManager.html
 

<!---HONumber=Oct15_HO3-->