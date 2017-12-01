
1. Откройте проект в Android Studio.

2. В Android Studio в **обозревателе проектов** откройте файл `ToDoActivity.java` и добавьте следующие операторы import:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Добавьте в класс **ToDoActivity** следующий метод:

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Login using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // login succeeded
                    createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    createTable();
                } else {
                    // login failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

    Этот код создает метод для обработки процесса проверки подлинности Google. В диалоговом окне отображается идентификатор пользователя, прошедшего проверку подлинности. Вы сможете продолжить, только если проверка подлинности выполнена успешно.

    > [!NOTE]
    > Если вы используете поставщик удостоверений, отличный от Google, измените значение, переданное в методе **login**, на одно из следующих значений: _MicrosoftAccount_, _Facebook_, _Twitter_ или _windowsazureactivedirectory_.

4. Добавьте в метод **onCreate** следующую строку после кода, который формирует экземпляр объекта `MobileServiceClient`.

    ```java
    authenticate();
    ```

    Этот вызов запускает процесс проверки подлинности.

5. Переместите оставшийся код после `authenticate();` в методе **onCreate** в новый метод **createTable**.

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. Чтобы перенаправление работало правильно, добавьте в файл `AndroidManifest.xml` фрагмент кода `RedirectUrlActivity`.

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. Добавьте `redirectUriScheme` в файл `build.gradle` приложения Android.

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. Добавьте `com.android.support:customtabs:23.0.1` в зависимости в файле `build.gradle`.

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. В меню **Запуск** щелкните **Запуск приложения**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений.

> [!WARNING]
> Упомянутая схема URL-адреса чувствительная к регистру. Убедитесь, что во всех вхождениях `{url_scheme_of_you_app}` используется один и тот же регистр.

После входа мобильное приложение должно работать без ошибок, а у вас должна быть возможность отправлять запросы в серверную службу и обновлять данные.
