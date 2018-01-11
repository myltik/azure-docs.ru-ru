
## <a name="set-up-your-project"></a>Настройка проекта

> Предпочитаете скачать этот пример проекта Android Studio? [Скачайте проект](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip) и перейдите к [настройке](#create-an-application-express), чтобы настроить пример кода перед выполнением.


### <a name="create-a-new-project"></a>Создание нового проекта 
1.  Откройте Android Studio и перейдите к: `File` > `New` > `New Project`.
2.  Присвойте имя приложению и щелкните `Next`.
3.  Выберите *API версии 21 или более поздней (Android версии 5.0)* и щелкните `Next`.
4.  Выйдите из раздела `Empty Activity`, щелкните `Next`, а затем — `Finish`.


### <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Добавление библиотеки проверки подлинности Майкрософт (MSAL) в проект
1.  Откройте Android Studio и перейдите к: `Gradle Scripts` > `build.gradle (Module: app)`.
2.  Скопируйте следующий код и вставьте его в раздел `Dependencies`.

```ruby  
compile ('com.microsoft.identity.client:msal:0.1.+') {
    exclude group: 'com.android.support', module: 'appcompat-v7'
}
compile 'com.android.volley:volley:1.0.0'
```

<!--start-collapse-->
### <a name="about-this-package"></a>Об этом пакете

Приведенный выше пакет устанавливает библиотеку проверки подлинности Майкрософт (MSAL). MSAL обрабатывает получение, кэширование и обновление маркеров пользователей, которые используются для доступа к API, защищенному конечной точкой Azure Active Directory версии 2.
<!--end-collapse-->

## <a name="create-your-applications-ui"></a>Создание пользовательского интерфейса приложения

1.  Откройте `activity_main.xml` (в `res` > `layout`).
2.  Измените макет действия `android.support.constraint.ConstraintLayout` или другой на `LinearLayout`.
3.  Добавьте свойство `android:orientation="vertical"` в узел `LinearLayout`.
4.  Скопируйте и вставьте следующий код в узел `LinearLayout`, заменив текущее содержимое:

```xml
<TextView
    android:text="Welcome, "
    android:textColor="#3f3f3f"
    android:textSize="50px"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="10dp"
    android:layout_marginTop="15dp"
    android:id="@+id/welcome"
    android:visibility="invisible"/>

<Button
    android:id="@+id/callGraph"
    android:text="Call Microsoft Graph"
    android:textColor="#FFFFFF"
    android:background="#00a1f1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="200dp"
    android:textAllCaps="false" />

<TextView
    android:text="Getting Graph Data..."
    android:textColor="#3f3f3f"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="5dp"
    android:id="@+id/graphData"
    android:visibility="invisible"/>

<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="0dip"
    android:layout_weight="1"
    android:gravity="center|bottom"
    android:orientation="vertical" >

    <Button
        android:text="Sign Out"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="15dp"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:textAllCaps="false"
        android:id="@+id/clearCache"
        android:visibility="invisible" />
</LinearLayout>
```

