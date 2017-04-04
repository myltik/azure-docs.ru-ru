
Чтобы зарегистрировать приложение для push-уведомлений посредством службы push-уведомлений Apple (APNS), необходимо создать сертификат push-уведомлений, идентификатор приложения и профиль подготовки для проекта на портале разработчика Apple.

Идентификатор приложения содержит параметры конфигурации, позволяющие приложению отправлять и получать push-уведомления. В эти параметры входит сертификат push-уведомлений, требуемый для аутентификации в службе APNS при отправке и получении push-уведомлений в приложении.

Дополнительные сведения об этих понятиях см. в официальной документации по [службе push-уведомлений Apple](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

## <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Создание файла запроса подписи сертификата для сертификата push-уведомлений
В этой статье описан процесс создания запроса на подпись сертификата, который создает сертификат push-уведомлений, используемый с APNS.

1. На компьютере Mac запустите средство Keychain Access. Его можно запустить из папки **Utilities** (Служебные программы) или **Other** (Другое) на панели запуска.

2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем выберите **Request a Certificate from a Certificate Authority...** (Запросить сертификат в центре сертификации...).

      ![Запрос сертификата](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Введите значения для параметров **User Email Address** (Адрес электронной почты пользователя) и **Common Name** (Общее имя).

4. Установите флажок **Saved to disk** (Сохранено на диск), а затем нажмите кнопку **Continue** (Продолжить). Оставьте поле **CA Email Address** (Адрес электронной почты ЦС) пустым, потому что оно не является обязательным.

      ![Сведения о сертификате](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Введите имя файла запроса на подпись сертификата (CSR) в поле **Save As** (Сохранить как).
5. Выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).

      ![Сохранение запроса на подпись сертификата](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

      При этом CSR-файл сохраняется в выбранном месте; (По умолчанию — на рабочем столе.) Запомните расположение, выбранное для этого файла.

## <a name="register-your-app-for-push-notifications"></a>Регистрация приложения для работы с push-уведомлениями
Создайте новый явный идентификатор приложения для вашего приложения в системе Apple, а также настройте его для получения push-уведомлений.  

1. Перейдите на [портал подготовки iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) в Apple Developer Center и выполните вход с использованием вашего идентификатора Apple.

2. Выберите **Identifiers** (Идентификаторы), а затем — **App IDs** (Идентификаторы приложений).

3. Нажмите кнопку **+**, чтобы зарегистрировать новое приложение.

      ![Регистрация нового приложения](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

4. Обновите для нового приложения следующие три поля и нажмите кнопку **Continue** (Продолжить).

   * **Name** (Имя). В разделе **App ID Description** (Описание идентификатора приложения) введите описательное имя своего приложения.

   * **Bundle Identifier** (Идентификатор пакета). В разделе **Explicit App ID** (Явный идентификатор приложения) введите в форму `<Organization Identifier>.<Product Name>` **идентификатор набора**, как упоминалось в [руководстве по распространению приложения](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Используемые значения должны соответствовать значениям в проекте XCode, Xamarin или Cordova для вашего приложения.

   * **Push Notifications** (Push-уведомления). В разделе **App Services** (Службы приложений) установите флажок **Push Notifications** (Push-уведомления).

     ![Регистрация идентификатора приложения](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

5. На **экране подтверждения идентификатора приложения** проверьте значения параметров. Затем нажмите кнопку **Register** (Зарегистрировать).

6. Отправив новый идентификатор приложения, вы увидите экран **Registration complete** (Регистрация выполнена). Нажмите кнопку **Готово**.

7. В центре разработчиков в разделе **App IDs** (Идентификаторы приложений) найдите только что созданный идентификатор и выберите его строку. После этого отобразятся сведения о приложении. В нижней части экрана нажмите кнопку **Edit** (Изменить).

8. Прокрутите до нижней части экрана и нажмите кнопку **Create Certificate...** (Создать сертификат...) в разделе **Development SSL Certificate** (SSL-сертификат разработки).

      ![SSL-сертификат разработки push-уведомлений](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

 Откроется помощник "Add iOS Certificate" (Добавление сертификата iOS).

   > [!NOTE]
   > В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации рабочего сертификата. Убедитесь, что при отправке уведомлений используется тот же тип сертификата.
   >

9. Щелкните **Choose File** (Выбрать файл) и перейдите в папку, в которую ранее был сохранен CSR-файл. Выберите **Generate** (Создать).

      ![Создание сертификата](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

10. После создания сертификата с помощью портала нажмите кнопку **Download** (Загрузить).

      ![Сертификат готов](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

       При этом сертификат подписи загружается и сохраняется на вашем компьютере в папке "Загрузки".

      ![Папка загрузки](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

   > [!NOTE]
   > По умолчанию скачанный файл сертификата разработки называется **aps_development.cer**.
   >
   >
11. Дважды щелкните скачанный сертификат push-уведомлений **aps_development.cer**. При этом новый сертификат устанавливается в Keychain, как на следующем снимке экрана.

       ![Keychain Access](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

   > [!NOTE]
   > Хотя имя вашего сертификата может отличаться, оно будет начинаться с префикса **Apple Development iOS Push Services**.
   >
   >
12. В программе **Keychain Access** выберите новый сертификат push-уведомлений, созданный в категории **Certificates** (Сертификаты). Щелкните **Export** (Экспорт), укажите имя файла, выберите формат **P12** и нажмите кнопку **Save** (Сохранить).

    Запишите имя файла и расположение экспортируемого сертификата в формате P12. Это необходимо для включения аутентификации с помощью APNS после отправки на классический портал Azure. Если формат **P12** недоступен, попробуйте перезапустить Keychain Access.

## <a name="create-a-provisioning-profile-for-the-app"></a>Создание профиля подготовки для приложения
1. На <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портале подготовки iOS</a> выберите **Provisioning Profiles** (Профили подготовки), а затем щелкните **All** (Все) и нажмите кнопку **+**, чтобы создать профиль. Запустится средство **Add iOS Provisiong Profile** (Добавление профиля подготовки для iOS).

      ![Профили подготовки](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. В разделе **Development** (Разработка) выберите **iOS App Development** (Разработка приложений для iOS) в качестве типа профиля подготовки и нажмите кнопку **Continue** (Продолжить).

3. Затем в раскрывающемся списке **App ID** (Идентификатор приложения) выберите идентификатор приложения, который вы только что создали, и нажмите кнопку **Continue** (Продолжить).

      ![Выбор идентификатора приложения](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. На экране **Select certificates** (Выбор сертификатов) выберите сертификат для разработки, используемый для подписывания кода, и нажмите кнопку **Continue** (Продолжить). Это сертификат подписи, а не только что созданный сертификат push-уведомлений.

       ![Signing certificate](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. Затем выберите **Devices** (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).

     ![Добавление профиля подготовки](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Наконец, укажите имя профиля в поле **Profile Name** (Имя профиля) и нажмите кнопку **Generate** (Создать).

      ![Присвоение имени профилю](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
