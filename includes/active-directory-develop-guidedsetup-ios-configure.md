## <a name="register-your-application"></a>Регистрация приложения
Приложение можно зарегистрировать одним из двух способов, которые описаны в следующих двух разделах.

### <a name="option-1-express-mode"></a>Вариант 1. Экспресс-режим
Теперь вам необходимо зарегистрировать приложение на *портале регистрации приложений Майкрософт*:
1. Зарегистрируйте свое приложение на [портале регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure).
2.  Введите имя для приложения и адрес электронной почты.
3.  Выберите параметр Guided Setup (Пошаговая настройка).
4.  Следуйте инструкциям, чтобы получить идентификатор приложения. Затем вставьте его в свой код.

### <a name="option-2-advanced-mode"></a>Вариант 2. Расширенный режим

1.  Перейдите на [портал регистрации приложений](https://apps.dev.microsoft.com/portal/register-app)
2.  Введите имя для приложения и адрес электронной почты.
3.  Убедитесь, что параметр Guided Setup (Пошаговая настройка) не выбран.
4.  Щелкните `Add Platform`, затем выберите `Native Application` и щелкните `Save`.
5.  Вернитесь в Xcode. В `ViewController.swift` замените строку, начинающуюся с '`let kClientID`', на зарегистрированный идентификатор приложения:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Щелкните <code>Info.plist</code>, удерживая клавишу CTRL, чтобы открыть контекстное меню, затем щелкните <code>Open As</code> > <code>Source Code</code>
.</li>
<li>
Добавьте следующий элемент в корневой узел <code>dict</code>:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Замените <i><code>[Your_Application_Id_Here]</code></i> зарегистрированным идентификатором приложения.
</li>
</ol>
