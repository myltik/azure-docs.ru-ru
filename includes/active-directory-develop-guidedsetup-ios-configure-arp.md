
## <a name="add-the-applications-registration-information-to-your-app"></a>Добавление в приложение сведений о его регистрации

На этом шаге вам нужно добавить в свой проект код приложения:

1.  В `ViewController.swift` замените строку, начинающуюся с '`let kClientID`', на следующую:
```swift
let kClientID = "[Enter the application Id here]"
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
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
            <string>msal[Enter the application Id here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
