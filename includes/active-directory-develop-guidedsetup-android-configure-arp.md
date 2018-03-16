
## <a name="add-the-applications-registration-information-to-your-app"></a>Добавление в приложение сведений о его регистрации

На этом шаге вам нужно добавить идентификатор клиента в свой проект.

1.  Откройте `MainActivity` (выберите `app` > `java` > *`{host}.{namespace}`*).
2.  Замените строку, начинающуюся с `final static String CLIENT_ID`, следующей:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Откройте: `app` > `manifests` > `AndroidManifest.xml`.
4. Добавьте следующее действие в узел `manifest\application`. Так вы зарегистрируете `BrowserTabActivity`, чтобы позволить операционной системе возобновить ваше приложение после завершения аутентификации.

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
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

