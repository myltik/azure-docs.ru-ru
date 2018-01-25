
## <a name="register-your-application"></a>Регистрация приложения
Приложение можно зарегистрировать одним из двух способов, которые описаны в следующих двух разделах.

### <a name="option-1-express-mode"></a>Вариант 1. Экспресс-режим
Чтобы быстро зарегистрировать приложение, сделайте следующее:
1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  В поле **Имя приложения** введите имя приложения.

3. Обязательно установите флажок **Guided Setup** (Интерактивная настройка) и нажмите кнопку **Создать**.

4. Следуйте инструкциям, чтобы получить идентификатор приложения. Затем вставьте его в свой код.

### <a name="option-2-advanced-mode"></a>Вариант 2. Расширенный режим
Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение, сделайте следующее:
1. Если вы еще не зарегистрировали приложение, перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app).
2. В поле **Имя приложения** введите имя приложения. 

3. Обязательно снимите флажок **Guided Setup** (Интерактивная настройка) и нажмите кнопку **Создать**.

4. Выберите **Добавление платформы**, **Собственное приложение**, а затем нажмите кнопку **Сохранить**.

5. В разделе **app** > **java** > **{узел}.{пространство_имен}** откройте `MainActivity`. 

6.  Замените заполнитель *[Enter the application Id here]* в следующей строке только что зарегистрированным идентификатором приложения:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. В разделе **app** > **manifests** откройте файл *AndroidManifest.xml*.

8. В узел `manifest\application` добавьте указанное ниже действие. Так вы зарегистрируете действие `BrowserTabActivity`, которое позволяет операционной системе возобновить работу приложения после завершения аутентификации.

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
<!-- Workaround for Docs conversion bug -->
9. В узле `BrowserTabActivity` замените заполнитель `[Enter the application Id here]` идентификатором приложения.
