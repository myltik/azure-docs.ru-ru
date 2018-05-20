## <a name="setting-up-your-ios-application"></a>Настройка приложения iOS

В этом разделе содержатся пошаговые инструкции по созданию проекта, который демонстрирует, как интегрировать приложение iOS (Swift) с *входом с учетной записью Майкрософт*. Это позволит приложению выполнять вызовы к веб-API, для которых требуется маркер.

> Хотите скачать этот пример проекта XCode вместо указанного выше проекта? [Скачайте проект](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) и перейдите к [настройке](#register-your-application), чтобы настроить пример кода перед выполнением.


## <a name="install-carthage-to-download-and-build-msal"></a>Установка Carthage для скачивания и сборки MSAL
Диспетчер пакетов Carthage используется в предварительной версии MSAL — он интегрируется с XCode, при этом корпорация Майкрософт может вносить изменения в библиотеку.

- Скачайте и установите последний выпуск Carthage [здесь](https://github.com/Carthage/Carthage/releases "URL-адрес для скачивания Carthage")

## <a name="creating-your-application"></a>Создание приложения

1.  Откройте в Xcode и выберите `Create a new Xcode project`.
2.  Выберите `iOS` > `Single view Application` и нажмите кнопку *Далее*.
3.  Введите название продукта и нажмите кнопку *Далее*.
4.  Выберите папку для создания приложения и нажмите кнопку *Создать*.

## <a name="build-the-msal-framework"></a>Сборка платформы MSAL

Для скачивания и сборки последней версии библиотек MSAL с помощью Carthage следуйте инструкциям ниже:

1.  Откройте терминал Bash и перейдите в корневую папку приложения
2.  Скопируйте и вставьте следующий код в окно терминала Bash, чтобы создать файл "Cartfile":

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Скопируйте и вставьте следующий код. Эта команда извлекает зависимости в папку Carthage/Checkouts, а затем создает библиотеку MSAL:
</li>
</ol>

```bash
carthage update
```

> Описанный выше процесс используется для скачивания и сборки библиотеки проверки подлинности Microsoft (MSAL). MSAL обрабатывает получение, кэширование и обновление маркеров пользователей, которые необходимы для доступа к API-интерфейсам, защищенным Azure Active Directory версии 2.

## <a name="add-the-msal-framework-to-your-application"></a>Добавление платформы MSAL в приложение
1.  В Xcode откройте вкладку `General`.
2.  Перейдите в раздел `Linked Frameworks and Libraries` и щелкните `+`.
3.  Выберите `Add other…`
4.  Выберите `Carthage` > `Build` > `iOS` > `MSAL.framework` и нажмите кнопку *Открыть*. Вы увидите, что в список добавлен `MSAL.framework`.
5.  Перейдите на вкладку `Build Phases` и щелкните значок `+`, затем выберите `New Run Script Phase`
6.  Добавьте следующее содержимое в *область сценария*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Добавьте следующий код в <code>Input Files</code>, щелкнув <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Создание пользовательского интерфейса приложения
Файл Main.storyboard должен быть создан автоматически, как часть шаблона проекта. Следуйте инструкциям для создания пользовательского интерфейса приложения:

1.  Щелкните `Main.storyboard`, удерживая клавишу CTRL, чтобы открыть контекстное меню, затем щелкните `Open As` > `Source Code`.
2.  Замените узел `<scenes>` на следующий код:

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign-Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```
