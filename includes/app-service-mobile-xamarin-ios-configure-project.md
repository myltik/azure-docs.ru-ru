###В Xamarin Studio

1. В Xamarin.Studio откройте **Info.plist** и обновите **Идентификатор пакета**, указав свой ранее созданный идентификатор.

    ![][121]

2. Прокрутите вниз до раздела **Фоновые режимы** и установите флажки **Разрешить фоновые режимы** и **Удаленные уведомления**.

    ![][122]

3. Дважды щелкните проект на панели решения, чтобы открыть **Параметры проекта**.

4.  Выберите** iOS Bundle Signing** в разделе **Сборка**, выберите соответствующее **Удостоверение** и созданный для данного проекта **Профиль подготовки**.

    ![][120]

    Таким образом, проект будет использовать новый профиль для подписи кода. Официальную документацию по подготовке устройств Xamarin см. в статье [Подготовка устройства Xamarin].

### В Visual Studio

1. В Visual Studio щелкните правой кнопкой мыши проект и выберите пункт **Свойства**.

3. На страницах свойств щелкните вкладку **Приложение iOS** и обновите **идентификатор**, указав свой ранее созданный ИД.

    ![][123]

4. На вкладке **iOS Bundle Signing** выберите соответствующее **Удостоверение** и созданный для данного проекта **Профиль подготовки**.

    ![][124]

    Таким образом, проект будет использовать новый профиль для подписи кода. Официальную документацию по подготовке устройств Xamarin см. в статье [Подготовка устройства Xamarin].

[120]: ./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png
[121]: ./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png
[122]: ./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png
[123]: ./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png
[124]: ./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png

[Подготовка устройства Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

<!---HONumber=August15_HO8-->