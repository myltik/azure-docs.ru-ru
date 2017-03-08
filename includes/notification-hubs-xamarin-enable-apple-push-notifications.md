

Чтобы зарегистрировать приложение для push-уведомлений посредством службы push-уведомлений Apple (APNS), необходимо создать новый push-сертификат, идентификатор приложения и профиль подготовки для проекта на портале разработчика Apple. Идентификатор приложения будет содержать параметры конфигурации, позволяющие приложению отправлять и получать push-уведомления. В эти параметры будет входить сертификат push-уведомлений, необходимый для проверки подлинности в службе push-уведомлений Apple (APNS) при отправке и получении push-уведомлений. Дополнительные сведения об этих понятиях см. в официальной документации по [службе push-уведомлений Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584).

#### <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Генерация файла запроса подписи сертификата для push-сертификата
Следующие действия используются для создания запроса подписи сертификата. Он будет использоваться для создания push-сертификата, используемого в APNS.

1. На компьютере Mac запустите средство Keychain Access. Его можно запустить из папки **Utilities** (Служебные программы) или **Other** (Другое) на панели запуска.
2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем выберите **Request a Certificate from a Certificate Authority...** (Запросить сертификат в центре сертификации...).
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Выберите ваши параметры **User Email Address** (Адрес электронной почты пользователя) и **Common Name** (Общее имя), установите флажок **Saved to disk** (Сохранено на диск), а затем нажмите кнопку **Continue** (Продолжить). Оставьте поле **CA Email Address** (Адрес электронной почты ЦС) пустым, поскольку оно не является обязательным.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Введите имя файла запроса подписи сертификата (CSR) в поле **Save As** (Сохранить как), выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      При этом CSR-файл сохраняется в выбранном месте; по умолчанию — на рабочем столе. Запомните расположение, выбранное для этого файла.

#### <a name="register-your-app-for-push-notifications"></a>Регистрация приложения для работы с push-уведомлениями
Создайте новый явный идентификатор приложения для вашего приложения в системе Apple, а также настройте его для получения push-уведомлений.  

1. Перейдите на [портал подготовки iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) в центре разработчиков Apple, выполните вход с использованием вашего идентификатора Apple, щелкните **Идентификаторы**, затем — **Идентификаторы приложений** и, наконец, нажмите кнопку **+**, чтобы зарегистрировать новое приложение.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)
2. Обновите для нового приложения следующие три поля и нажмите кнопку **Продолжить**.
   
   * **Name** (Имя). В разделе **App ID Description** (Описание идентификатора приложения) в поле **Name** (Имя) введите описательное имя своего приложения.
   * **Bundle Identifier** (Идентификатор набора). В разделе **Explicit App ID** (Явный идентификатор приложения) введите в форму `<Organization Identifier>.<Product Name>` **идентификатор набора**, как упоминалось в [руководстве по распространению приложения](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Используемые значения должны соответствовать значениям в проекте XCode, Xamarin или Cordova для вашего приложения.
   * **Push Notifications** (push-уведомления). В разделе **App Services** (Службы приложений) установите флажок **Push Notifications** (push-уведомления).
     
     ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
3. На экране подтверждения идентификатора приложения проверьте значения параметров и после этого нажмите кнопку **Отправить**
4. После отправки нового идентификатора приложения вы увидите экран **Регистрация выполнена** . Нажмите кнопку **Done**(Готово).
5. В центре разработчиков в разделе App IDs (Идентификаторы приложений) найдите только что созданный идентификатор и щелкните его строку. Если щелкнуть идентификатор приложения, отобразятся сведения о приложении. В нижней части экрана нажмите кнопку **Изменить** .
6. Прокрутите до нижней части экрана и нажмите кнопку **Create Certificate...** (Создать сертификат...) в разделе **Development Push SSL Certificate** (SSL-сертификат разработки push-уведомлений).
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
       This will display the "Add iOS Certificate" assistant.
   
   > [!NOTE]
   > В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Только убедитесь, что при отправке уведомлений используется тот же тип сертификата.
   > 
   > 
7. Щелкните **Выбрать файл**, перейдите в папку, в которую ранее был сохранен CSR-файл. Нажмите кнопку **Создать**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
8. После создания сертификата с помощью портала нажмите кнопку **Загрузить** .
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
       This downloads the signing certificate and saves it to your computer in your Downloads folder.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > По умолчанию скачанный файл сертификата разработки называется **aps_development.cer**.
   > 
   > 
9. Дважды щелкните скачанный сертификат push-уведомлений **aps_development.cer**. При этом новый сертификат устанавливается в Keychain, как показано ниже:
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > Имя вашего сертификата может отличаться, но оно будет начинаться с префикса **Apple Development iOS Push Services:**.
   > 
   > 
10. В программе Keychain Access щелкните при нажатой клавише CTRL новый сертификат push-уведомлений, созданный в категории **Сертификаты**. Щелкните **Export** (Экспорт), укажите имя файла, выберите формат **.p12** и нажмите кнопку **Save** (Сохранить).
    
    Запишите имя файла и расположение экспортируемого сертификата в формате P12. Он будет использоваться для включения проверки подлинности с помощью APNS после отправки на классический портал Azure.

#### <a name="create-a-provisioning-profile-for-the-app"></a>Создание профиля подготовки для приложения
1. На <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портале подготовки iOS</a> выберите **Provisioning Profiles** (Профили подготовки), а затем щелкните **All** (Все) и нажмите кнопку **+**, чтобы создать новый профиль. Запустится мастер **Add iOS Provisiong Profile** (Добавление профиля подготовки для iOS).
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. В разделе **Development** (Разработка) выберите пункт **iOS App Development** (Разработка приложений для iOS) в качестве типа профиля подготовки и нажмите кнопку **Continue** (Продолжить).
3. Далее выберите идентификатор приложения, который вы только что создали, из раскрывающегося списка **App ID** (Идентификатор приложения) и нажмите кнопку **Continue** (Продолжить).
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. На экране **Выбор сертификатов** выберите обычный сертификат для разработки, используемый для подписывания кода, и нажмите кнопку **Продолжить**. Это сертификат подписи, а не только что созданный сертификат push-уведомлений.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Затем выберите **Devices** (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Наконец, укажите имя профиля в поле **Profile Name** (Имя профиля) и нажмите кнопку **Generate** (Создать).
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)



<!--HONumber=Feb17_HO1-->


