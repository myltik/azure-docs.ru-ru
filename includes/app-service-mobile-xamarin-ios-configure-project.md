####Настройка проекта iOS в Xamarin Studio

1. В Xamarin.Studio откройте **Info.plist** и обновите **Идентификатор пакета**, указав идентификатор пакета, который вы создали ранее, используя идентификатор нового приложения.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. Прокрутите вниз до раздела **Фоновые режимы** и установите флажки **Разрешить фоновые режимы** и **Удаленные уведомления**.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. Дважды щелкните проект на панели решения, чтобы открыть **Параметры проекта**.

4.  Выберите** iOS Bundle Signing** в разделе **Сборка**, выберите соответствующее **Удостоверение** и созданный для данного проекта **Профиль подготовки**.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    Таким образом, проект будет использовать новый профиль для подписи кода. Официальную документацию по подготовке устройств Xamarin см. в статье [Подготовка устройства Xamarin].

####Настройка проекта iOS в Visual Studio

1. В Visual Studio щелкните правой кнопкой мыши проект и выберите пункт **Свойства**.

3. На страницах свойств щелкните вкладку **Приложение iOS** и обновите **идентификатор**, указав свой ранее созданный ИД.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

4. На вкладке **iOS Bundle Signing** выберите соответствующее **Удостоверение** и созданный для данного проекта **Профиль подготовки**.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Таким образом, проект будет использовать новый профиль для подписи кода. Официальную документацию по подготовке устройств Xamarin см. в статье [Подготовка устройства Xamarin].


[Подготовка устройства Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

<!---HONumber=AcomDC_1125_2015-->