
1. В **обозревателе проектов** в Android Studio откройте файл ToDoActivity.java и добавьте следующие операторы import:

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
2. Добавьте в класс **ToDoActivity** следующий метод:

        // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
        public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;
 
        private void authenticate() {
            // Login using the Google provider.
            mClient.login("Google", "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
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

    При этом создается новый метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с использованием имени входа в Google. В диалоговом окне отображается идентификатор пользователя, прошедшего проверку подлинности. Без успешной проверки подлинности продолжение невозможно.

    > [!NOTE]
    > Если вы используете поставщик удостоверений, отличный от Google, измените значение, переданное в методе **login**, выше на одно из следующих: _MicrosoftAccount_, _Facebook_, _Twitter_ или _windowsazureactivedirectory_.

3. Добавьте в метод **onCreate** следующую строку после кода, который формирует экземпляр объекта `MobileServiceClient`.

        authenticate();

    Этот вызов запускает процесс проверки подлинности.
4. Переместите оставшийся код после `authenticate();` в методе **onCreate** в новый метод **createTable**. Он выглядит следующим образом:

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

5. Добавьте в файл _AndroidManifest.xml_ следующий фрагмент _RedirectUrlActivity_, чтобы обеспечить перенаправление.
 
        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="{url_scheme_of_your_app}"
                    android:host="easyauth.callback"/>
            </intent-filter>
        </activity>

6.  Добавьте redirectUriScheme к _build.gradle_ приложения Android.
 
        android {
            buildTypes {
                release {
                    // … …
                    manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
                }
                debug {
                    // … …
                    manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
                }
            }
        }

7. Добавьте com.android.support:customtabs:23.0.1 к зависимостям в build.gradle:

      dependencies {        // ...        compile 'com.android.support:customtabs:23.0.1'    }

8. В меню **Запуск** щелкните **Запуск приложения**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений.

После входа мобильное приложение должно работать без ошибок, а у вас должна быть возможность отправлять запросы в серверную службу и обновлять данные.
