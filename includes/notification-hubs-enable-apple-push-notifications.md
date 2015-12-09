

##Создание файла запроса на подпись сертификата

Служба push-уведомлений Apple (APNS) использует сертификаты для аутентификации push-уведомлений. Следуйте приведенным указаниям, чтобы создать сертификата push-уведомлений, необходимый для отправки и получения уведомлений. Дополнительные сведения об этих понятиях см. в официальной документации по [службе push-уведомлений Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Создайте файл запроса подписи сертификата (CSR), используемый Apple для создания подписанного сертификата push-уведомлений.

1. На компьютере Mac запустите средство Keychain Access. Его можно запустить из папки **Utilities** (Служебные программы) или **Other** (Другое) на панели запуска.

2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем щелкните **Request a Certificate from a Certificate Authority...** (Запросить сертификат в центре сертификации...).

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Выберите ваши параметры **User Email Address** (Адрес электронной почты пользователя) и **Common Name** (Общее имя), убедитесь, что установлен флажок **Saved to disk** (Сохранено на диск), а затем щелкните **Continue** (Продолжить). Оставьте поле **CA Email Address** (Адрес электронной почты ЦС) пустым, поскольку оно не является обязательным.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Введите имя файла запроса подписи сертификата (CSR) в поле **Save As** (Сохранить как), выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	При этом CSR-файл сохраняется в выбранном месте; по умолчанию — на рабочем столе. Запомните расположение, выбранное для этого файла.

Затем вы зарегистрируете ваше приложение в Apple, включите push-уведомления и передадите этот экспортированный CSR-файл для создания сертификата push-уведомлений.

##Регистрация приложения для работы с push-уведомлениями

Чтобы иметь возможность отправлять push-уведомления в приложение iOS, необходимо зарегистрировать ваше приложение в системе Apple, а также зарегистрировать его для получения push-уведомлений.

1. Если ваше приложение еще не зарегистрировано, перейдите на <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портал подготовки iOS</a> в центре разработчиков Apple, выполните вход с использованием вашего идентификатора Apple, щелкните **Идентификаторы**, а затем — **Идентификаторы приложений** и, наконец, нажмите кнопку **+**, чтобы зарегистрировать новое приложение.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)


2. Обновите для нового приложения следующие три поля и нажмите кнопку **Continue** (Продолжить).

	* **Name** (Имя). В разделе **App ID Description** (Описание идентификатора приложения) в поле **Name** (Имя) введите описательное имя своего приложения.
	
	* **Bundle Identifier** (Идентификатор набора). В разделе **Explicit App ID** (Явный идентификатор приложения) введите в форму `<Organization Identifier>.<Product Name>` **идентификатор набора**, как упоминалось в [руководстве по распространению приложения](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Используемые значения *Organization Identifier* (Идентификатор организации) и *Product Name* (Имя продукта) должны соответствовать идентификатору организации и имени продукта, которые вы укажете при создании проекта XCode. На следующем снимке экрана *NotificationHubs* используется как идентификатор организации, а *GetStarted* — как имя продукта. Соответствие значениям, которые будут использоваться в проекте XCode, позволит вам использовать правильный профиль публикации в XCode.
	
	* **Push Notifications** (Push-уведомления). В разделе **App Services** (Службы приложений) установите флажок **Push Notifications** (Push-уведомления).

	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

   	Будет создан идентификатор вашего приложения и запрошено подтверждение информации. Нажмите кнопку **Submit** (Отправить).


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-confirm-new-appid.png)


   	После нажатия кнопки **Submit** (Отправить) вы увидите экран **Registration complete** (Регистрация выполнена), представленный ниже. Нажмите кнопку **Done** (Готово).


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


3. В центре разработчиков в разделе App IDs (Идентификаторы приложений) найдите только что созданный идентификатор и щелкните его строку.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

   	Если щелкнуть идентификатор приложения, отобразятся сведения о приложении. В нижней части экрана нажмите кнопку **Edit** (Изменить).

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Прокрутите до нижней части экрана и нажмите кнопку **Create Certificate...** (Создать сертификат...) в разделе **Development Push SSL Certificate** (SSL-сертификат разработки push-уведомлений).

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	Откроется помощник "Add iOS Certificate" (Добавление сертификата iOS).

    > [AZURE.NOTE]В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Только убедитесь, что при отправке уведомлений используется тот же тип сертификата.

5. Щелкните **Choose File** (Выбрать файл), перейдите к папке, где был сохранен CSR-файл, созданный в первом задании, а затем щелкните **Generate** (Создать).

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. После создания сертификата с помощью портала нажмите кнопку **Download** (Загрузить) и щелкните **Done** (Готово).

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	При этом сертификат загружается и сохраняется на вашем компьютере в папке "Загрузки".

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]По умолчанию загруженный файл сертификата разработки называется **aps_development.cer**.

7. Дважды щелкните скачанный сертификат push-уведомлений **aps_development.cer**.

   	При этом новый сертификат устанавливается в Keychain, как показано ниже:

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]Имя вашего сертификата может отличаться, но оно будет начинаться с префикса **Apple Development iOS Push Services:**.

8. В программе Keychain Access щелкните правой кнопкой мыши новый сертификат push-уведомлений, созданный в категории **Certificates** (Сертификаты). Щелкните **Export** (Экспорт), укажите имя файла, выберите формат **.p12** и нажмите кнопку **Save** (Сохранить).

	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

	Запишите имя файла и расположение экспортируемого сертификата в формате P12. Он будет использоваться для проверки подлинности с помощью имен точек доступа.

	>[AZURE.NOTE]В этом учебнике создается файл Quickstart.p12. Имя файла и расположение могут отличаться.


##Создание профиля подготовки для приложения

1. На <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портале подготовки iOS</a> выберите **Профили подготовки**, затем щелкните **Все** и нажмите кнопку **+**, чтобы создать новый профиль. Запустится мастер **Add iOS Provisiong Profile** (Добавление профиля подготовки для iOS).

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Выберите элемент **Разработка приложений для iOS** в разделе **Разработка** в качестве типа профиля подготовки и нажмите кнопку **Продолжить**.


3. Далее выберите идентификатор приложения, который вы только что создали, из раскрывающегося списка **App ID** (Идентификатор приложения) и нажмите кнопку **Continue** (Продолжить).

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. На экране **Select certificates** (Выбор сертификатов) выберите обычный сертификат для разработки, используемый для подписывания кода, и нажмите кнопку **Continue** (Продолжить). Это не только что созданный сертификат push-уведомлений.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. Затем выберите **Devices** (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Наконец, выберите имя профиля в поле **Profile Name** (Имя профиля) и щелкните **Generate** (Создать).

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)


7. Когда новый профиль подготовки будет создан, загрузите и установите его на компьютер с Xcode. Затем нажмите кнопку **Done** (Готово).

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

<!---HONumber=AcomDC_1203_2015-->