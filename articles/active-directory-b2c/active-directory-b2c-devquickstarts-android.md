<properties
	pageTitle="Предварительная версия Azure AD B2C: вызов веб-API из приложения Android | Microsoft Azure"
	description="В этой статье показано, как создать приложение Android ";Список дел";, вызывающее веб-API Node.js с помощью токенов носителей OAuth 2.0. Как приложение Android, так и веб-API используют Azure AD B2C для управления удостоверениями пользователей и проверки подлинности пользователей."
	services="active-directory-b2c"
	documentationCenter="android"
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Предварительная версия Azure AD B2C: вызов веб-API из приложения Android

Azure Active Directory (Azure AD) B2C позволяет добавлять в веб-API и приложения Android эффективные функции для самостоятельного управления удостоверениями. Это можно сделать, выполнив несколько простых действий. В этой статье показано, как создать приложение Android "Список дел", вызывающее веб-API Node.js с помощью токенов носителей OAuth 2.0. Как приложение Android, так и веб-API используют Azure AD B2C для управления удостоверениями пользователей и проверки подлинности пользователей.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Для работы с этим кратким руководством требуется веб-API, защищенный службой Azure AD с B2C. Мы создали для вас файлы .NET и Node.js. В этом пошаговом руководстве предполагается, что пример веб-API Node.js уже настроен. Дополнительные сведения см. в [руководстве по веб-API Azure AD B2C для Node.js](active-directory-b2c-devquickstarts-api-node.md).

Клиентские приложения для Android, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL). Единственное предназначение ADAL — упростить процесс получения маркеров доступа. Чтобы показать, насколько это просто, в этом руководстве описывается создание приложения "Список дел" для Android, которое:

- получает маркеры доступа для вызова API приложения "Список дел" с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx);
- получает пользовательские списки дел;
- выполняет выход из системы для пользователей.

> [AZURE.NOTE] В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности. Чтобы изучить основы Azure AD B2C, прочитайте [руководство по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех ваших пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md), прежде чем продолжить.

## Создание приложения

Затем необходимо создать приложение в каталоге B2C. Таким образом в Azure AD поступят сведения, необходимые для безопасного взаимодействия с вашим приложением. В этом случае приложение и веб-API представлены одним **идентификатором приложения**, так как они включают в себя одно приложение логики. Создайте приложение, выполнив [эти указания](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

- Включите в приложение **веб-приложение** или /**веб-API**.
- Введите `urn:ietf:wg:oauth:2.0:oob` в качестве значения **URL-адреса ответа**. Это URL-адрес по умолчанию для данного примера кода.
- Создайте для своего приложения **секрет приложения** и скопируйте его. Оно понадобится вам позднее. Обратите внимание, что перед использованием это значение должно быть [экранировано для XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
- Скопируйте **идентификатор приложения**, назначенный приложению. Этот идентификатор также потребуется позднее.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Создание политик

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Это приложение содержит три вида идентификации: регистрация, вход и вход с помощью учетной записи Facebook. Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех политик обязательно выполните указанные ниже действия.

- В политике регистрации выберите **Отображаемое имя** и другие атрибуты регистрации.
- В каждой политике выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. У него должен быть префикс `b2c_1_`. Эти имена политик понадобятся вам через некоторое время.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к созданию приложения.

Обратите внимание, что в данной статье не рассматривается использование политик, которые вы только что создали. Дополнительные сведения о работе политик в Azure AD B2C см. в [руководстве по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Загрузка кода

Код этого учебника [размещен на портале GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Чтобы выполнить сборку примера, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). Ее также можно клонировать:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **Для работы с этим руководством необходимо скачать схему.** Так как полнофункциональное приложение для Android сложно реализовать, схема содержит UX-код, который будет выполнен после завершения работы с данным руководством. Это позволит разработчикам сэкономить время. UX-код не связан с темой добавления B2C в приложение для Android.

Кроме того, можно скачать готовое приложение [в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) или получить его из ветви `complete` того же репозитория.

Для создания с помощью Maven можно использовать файл `pom.xml` на верхнем уровне.

  1. Выполните действия, описанные в [разделе предварительных требований по настройке Maven для Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Установите эмулятор с пакетом SDK 21.
  3. Перейдите в корневую папку, куда клонирован репозиторий.
  4. Выполните команду `mvn clean install`.
  5. Перейдите в каталог примера краткого руководства `cd samples\hello`.
  6. Выполните команду `mvn android:deploy android:run`.

Приложение будет запущено. Введите учетные данные тестового пользователя.

Помимо архивного пакета Android (AAR), будут также отправлены архивные пакеты Java (JAR).

## Скачивание библиотеки ADAL для Android и добавление ее в рабочую область платформы Android Studio

В проекте Android эту библиотеку можно использовать несколькими способами:

* ее можно импортировать на платформу Eclipse в виде исходного кода и связать с приложением;
* в Android Studio можно использовать пакет AAR и сослаться на двоичные файлы.

### Вариант 1. Двоичные файлы через Gradle (рекомендуется)

Двоичные файлы можно получить из центрального репозитория Maven. Пакет AAR можно включить в проект Android Studio (например в `build.gradle`) следующим образом:

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### Вариант 2. AAR через Maven

Если вы используете подключаемый модуль `m2e` в Eclipse, то можно задать зависимость в файле `pom.xml`:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### Вариант 3. Исходные файлы через Git (последнее средство)

Чтобы получить исходный код пакета SDK через Git, введите:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Используйте **конвергенцию** ветви.

## Настройка файла конфигурации

Для настройки проекта Android следует использовать конфигурацию, настроенную ранее на портале B2C.

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
- `SCOPES` — области, которые можно передавать на сервер и необходимо запросить с сервера, когда пользователь выполняет вход. В предварительной версии B2C нужно передать `client_id`. Но в будущем эта область изменится на `read scopes`. После этого документ будет обновлен.
- `ADDITIONAL_SCOPES` — дополнительные области, которые могут потребоваться приложению. Они будут использоваться в будущем.
- `CLIENT_ID` — идентификатор приложения, полученный с портала.
- `REDIRECT_URL` — перенаправление, в которое маркер будет передан обратно.
- `EXTRA_QP` — любые дополнительные сведения, которые необходимо передать на сервер в формате URL-адреса.
- `FB_POLICY` — вызываемая политика. Это самая важная часть этого руководства.
- `EMAIL_SIGNIN_POLICY` — вызываемая политика. Это самая важная часть этого руководства.
- `EMAIL_SIGNUP_POLICY` — вызываемая политика. Это самая важная часть этого руководства.

## Добавление ссылок на библиотеку ADAL для Android в проект

> [AZURE.NOTE]	В ADAL для Android для вызова проверки подлинности используется модель на основе намерений. Намерения работают, "накладываясь" на приложение. Весь этот пример (как и использование ADAL для Android в целом) связан с управлением намерениями и передачей сведений между ними.

Прежде всего, необходимо передать в Android сведения о макете приложения и намерениях, которые необходимо использовать. Подробнее эти намерения описываются ниже в этом руководстве.

Добавьте в файл проекта `AndroidManifest.xml` следующие намерения:

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

Как видите, определено пять действий. Они все будут использоваться.

- Действие `AuthenticationActivity` передается из библиотеки ADAL и обеспечивает веб-представление для входа.
- Действие `LoginActivity` отображает ваши политики входа и кнопки для каждой политики.
- Действие `SettingsActivity` используется для изменения параметров приложения во время выполнения.
- Действие `AddTaskActivity` можно использовать для добавления задач в REST API, защищенный службой Azure AD.
- `ToDoActivity` — основное действие, отображающее задачи.

## Создание действия входа

Создайте основное действие и присвойте ему имя `LoginActivity`.

Создайте файл с именем `LoginActivity.java`.

Затем инициализируйте это действие и добавьте несколько кнопок для управления пользовательским интерфейсом. Если вы писали код для Android ранее, эти действия вам известны.

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
Вы создали кнопки, вызывающие намерение `ToDoActivity` (которое вызывает ADAL, если необходим маркер). Это можно сделать, используя действие в качестве ссылки и дополнительного параметра. Этот дополнительный параметр передается при помощи метода `intent.putExtra()`. Параметр `"thePolicy"` определяется на основе значения, указанного в файле `Constants.java`. Таким образом, вы передаете в намерение сведения о том, какую политику нужно вызывать в процессе проверки подлинности.

## Создание действия параметров

Это действие, заполняющее пользовательский интерфейс параметров.

Создайте файл `SettingsActivity.java` для простых операций создания, чтения, обновления и удаления (CRUD).

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

## Создание действия добавления задачи

Это действие можно использовать для добавления задачи в конечную точку REST API.

Создайте файл `AddTaskActivity.java` и запишите следующий код.

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

## Создание действия списка дел

Это главное действие. Оно позволяет получить маркер для политики из Azure AD, а затем использовать его для вызова сервера REST API задачи.

Создайте файл `ToDoActivity.java` и запишите следующий код. (Вызовы описаны ниже.)

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


 Возможно, вы заметили, что здесь используются методы, которые еще не написаны. К ним относятся `updateLoggedInUser()`, `clearSessionCookie()` и `getTasks()`. Инструкции по их написанию описаны ниже. На данном этапе ошибки в Android Studio можно игнорировать.

Объяснение параметров:

  - `SCOPES` — это обязательный параметр. Он представляет области, доступ к которым вы пытаетесь запросить. В предварительной версии B2C этот параметр представляет собой то же самое, что и `client_id`, но в будущем это изменится.
  - `POLICY` — политика, которая требуется при проверке подлинности пользователя.
  - `CLIENT_ID` — обязательный параметр, который передается с портала Azure AD.
  - `redirectUri` — URI перенаправления, в качестве которого можно задать имя пакета. Он не требуется для вызова `acquireToken`.
  - `getUserInfo()` — метод, с помощью которого можно определить, находится ли пользователь в кэше. В параметре также описывается, как уведомлять пользователя, если он не найден или его маркер доступа недопустимый. Этот метод описывается ниже в этом руководстве.
  - Параметр `PromptBehavior.always` указывает на необходимость запроса учетных данных, а не получения их из кэша или файла cookie.
  - Параметр `Callback` вызывается после обмена кода авторизации на маркер. Функция обратного вызова получит объект `AuthenticationResult`, содержащий маркер доступа, сведения о дате окончания срока действия и маркере идентификатора.

> [AZURE.NOTE]	Приложение корпоративного портала Microsoft Intune выступает в качестве брокера и может устанавливаться на устройство пользователей. Это приложение предоставляет единый вход (SSO) для доступа ко всем приложениям на устройстве. Разработчики должны разрешить использование Intune. При наличии одной учетной записи, созданной в приложении Authenticator, ADAL для Android будет использовать учетную запись брокера. Чтобы использовать брокер, разработчику необходимо зарегистрировать специальный `redirectUri`. `redirectUri` имеет формат msauth://packagename/Base64UrlencodedSignature. Вы можете получить `redirectUri` для приложения с помощью сценария `brokerRedirectPrint.ps1` или вызова API `mContext.getBrokerRedirectUri()`. Подпись связана с сертификатами подписи из магазина Google Play.

 Вы можете пропустить пользователя broker с помощью следующей команды:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Чтобы упростить это краткое руководство B2C, пользователь broker пропущен в примере кода.

Теперь создайте вспомогательные методы, которые в результате вызовов проверки подлинности в API задачи получают только маркер.

В том же файле `ToDoActivity.java` запишите следующий код.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Кроме того, добавьте методы, которые будут устанавливать и получать значение `AuthenticationResult` (в котором содержится ваш маркер) в глобальных `Constants`. Эти методы необходимо добавить, несмотря на то, что в потоках файла `ToDoActivity.java` используется `sResult`. В противном случае другие действия не смогут выполняться из-за отсутствия доступа к маркеру (например не будет добавлена задача в `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## Создание метода для возврата идентификатора пользователя

ADAL для Android представляет пользователя в виде объекта `UserIdentifier`. Этот объект управляет пользователем. Его можно использовать, чтобы определить, используется ли в вызовах один и тот же пользователь. На основе этих сведений вы будете знать, что вам можно положиться на кэш, а не отправлять новый вызов на сервер. Чтобы облегчить эту задачу, мы создали метод `getUserInfo()`, возвращающий объект `UserIdentifier`. Его можно использовать с методом `acquireToken()`. Мы также создали метод `getUniqueId()`, возвращающий идентификатор объекта `UserIdentifier` в кэше.

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

### Написание вспомогательных методов

Теперь напишите некоторые вспомогательные методы, которые помогут очистить файлы cookie и предоставить `AuthenticationCallback`. Эти методы используются в примере, чтобы убедиться в том, что платформа очищена для вызова действия `ToDo`.

В том же файле `ToDoActivity.java` запишите следующий код.

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

## Вызов API задачи

Теперь, когда действие готово к получению маркеров, вы можете написать API для доступа к серверу задач.

Метод `getTasks` возвращает массив, представляющий задачи на сервере.

Начните с метода `getTasks`.

В том же файле `ToDoActivity.java` запишите следующий код.

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

Напишите также метод, инициализирующий таблицы при первом запуске.

В том же файле `ToDoActivity.java` запишите следующий код.

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

Как видите, для работы кода требуются некоторые дополнительные методы. Давайте их запишем.

### Создание генератора URL-адресов конечных точек

Вам необходимо сгенерировать URL-адрес конечной точки, к которому вы будете подключаться. Сделайте это в файле того же класса.

**В том же файле** `ToDoActivity.java` запишите следующий код.

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


Обратите внимание, что вы добавили маркер доступа к запросу в коде, описанном в следующем разделе.

## Написание методов UX

Для работы приложения для Android требуются обратные вызовы. Это `createAndShowDialog` и `onResume()`. Если вы писали код для Android ранее, эти действия вам известны.

В том же файле `ToDoActivity.java` запишите следующий код.

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

Теперь перейдите к обратным вызовам диалогового окна.

В том же файле `ToDoActivity.java` запишите следующий код.

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

Теперь у вас есть файл `ToDoActivity.java`, который компилируется. На этом этапе должен компилироваться и весь проект.

## Запуск примера приложения

Наконец, создайте и запустите приложение в Android Studio или Eclipse. Зарегистрируйтесь в приложении или войдите в него. Создайте задачи для пользователя, выполнившего вход. Выйдите и снова войдите от имени другого пользователя. Затем создайте задачи для этого пользователя.

Обратите внимание, что в API хранятся задачи для каждого пользователя, так как API извлекает удостоверение пользователя из получаемого маркера доступа.

Готовый пример [предоставляется в виде ZIP-файла](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## Важные сведения


### Шифрование

ADAL шифрует маркеры и по умолчанию хранит их в `SharedPreferences`. Вы можете подробно рассмотреть класс `StorageHelper`. В Android введено безопасное хранилище закрытых ключей ** AndroidKeyStore 4.3 (API уровня 18)**. Библиотека ADAL использует это хранилище при работе с API уровня 18 и выше. Если требуется использовать ADAL с более ранними версиями пакета SDK, необходимо предоставить секретный ключ в `AuthenticationSettings.INSTANCE.setSecretKey`.

### Файлы cookie сеанса в веб-представлении

После закрытия приложения веб-представление Android не очищает файлы cookie сеанса. Очистить их можно с помощью следующего примера кода.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[Дополнительные сведения о файлах cookie](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

<!---HONumber=AcomDC_0420_2016-->