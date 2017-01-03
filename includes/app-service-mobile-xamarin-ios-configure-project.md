#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Настройка проекта iOS в Xamarin Studio
1. В Xamarin.Studio откройте **Info.plist** и обновите поле **Bundle Identifier** (Идентификатор пакета), указав идентификатор пакета, созданный ранее с помощью идентификатора нового приложения.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Прокрутите вниз до раздела **Background Modes** (Фоновые режимы). Установите флажки **Enable Background Modes** (Разрешить фоновые режимы) и **Remote notifications** (Удаленные уведомления).

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Дважды щелкните проект на панели решения, чтобы открыть **параметры проекта**.
4. В разделе **Build** (Сборка) выберите **iOS Bundle Signing** (Подпись пакета iOS), а затем выберите нужное удостоверение и созданный для этого проекта профиль подготовки в соответствующих полях.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Таким образом, проект будет использовать новый профиль для подписи кода. Официальную документацию по подготовке устройств Xamarin см. в статье [Device Provisioning] (Подготовка устройства).

#### <a name="configure-the-ios-project-in-visual-studio"></a>Настройка проекта iOS в Visual Studio
1. В Visual Studio щелкните правой кнопкой мыши проект и выберите пункт **Свойства**.
2. На страницах свойств щелкните вкладку **Приложение iOS** и обновите идентификатор в поле **Идентификатор**, указав свой ранее созданный ИД.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. На вкладке **iOS Bundle Signing** (Подпись пакета iOS) выберите нужное удостоверение и созданный для этого проекта профиль подготовки в соответствующих полях.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Таким образом, проект будет использовать новый профиль для подписи кода. Официальную документацию по подготовке устройств Xamarin см. в статье [Device Provisioning] (Подготовка устройства).
4. Дважды щелкните Info.plist, чтобы открыть этот элемент, а затем включите **RemoteNotifications** в разделе **Background Modes** (Фоновые режимы).

[Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/ (Подготовка устройства)


<!--HONumber=Dec16_HO2-->


