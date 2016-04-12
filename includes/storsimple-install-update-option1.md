<!--author=SharS last changed: 03/17/2016-->

#### Загрузка исправления

Для загрузки обновления программного обеспечения выполните следующие действия.

1. Запустите Internet Explorer и откройте страницу [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Если вы впервые используете каталог Центра обновления Майкрософт на этом компьютере, нажмите кнопку **Установить**, когда будет предложено установить надстройку каталога Центра обновления Майкрософт. ![Установка каталога](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

3. В поле поиска каталога Центра обновления Майкрософт введите номер необходимого исправления в базе данных (KB), например **3063418**, а затем нажмите кнопку **Найти**.

4. Вы увидите пакет **StorSimple Update 1.2 Appliance Update**. Щелкните **Добавить**. Пакет обновлений будет добавлен в вашу корзину.

5. Найдите дополнительные исправления, перечисленные в приведенной выше таблице (**3043005** и **3063416**) и добавьте каждое из них в корзину.

5. Щелкните **Просмотреть корзину**.

    ![Просмотр корзины](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

6. Щелкните элемент **Загрузить**. Введите или **выберите** локальное расположение, в которое хотите скачать файлы. Обновления скачиваются в указанное расположение и помещаются во вложенную папку с тем же именем, что и имя обновления. Этот каталог также можно скопировать в сетевую папку, которая доступна с вашего устройства.

>   [AZURE.NOTE]
Исправления должны быть доступны с обоих контроллеров, чтобы можно было получить любые возможные сообщения об ошибке от однорангового контроллера.

#### Установка и проверка исправлений обычного режима
Выполните следующие действия для установки и проверки обычных исправлений. Если вы уже установили их с помощью портала Azure, перейдите к [установке и проверке исправлений режима обслуживания](#to-install-and-verify-maintenance-mode-hotfixes).

1. Чтобы установить обновление для ПО, откройте интерфейс Windows PowerShell на последовательной консоли своего устройства StorSimple. Подробные инструкции см. в разделе [Использование PuTTY для подключения к последовательной консоли устройства](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). В командной строке нажмите клавишу **ВВОД**.

4. Выберите **Вариант 1**, чтобы войти на устройство с правами на полный доступ.

5. Чтобы установить пакет обновления, в командной строке введите следующую команду:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    В указанной выше команде вместо DNS в адресе общей папки укажите IP-адрес. Параметр "credential" используется только для доступа к общему ресурсу с проверкой подлинности.

	Для доступа к общим ресурсам рекомендуем использовать параметр учетных данных. Даже те общие ресурсы, которые доступны для всех, обычно закрыты для пользователей, не прошедших проверку подлинности.

    Пример выходных данных этой команды показан ниже.

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John

        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````

6. Введите **Y**, когда будет предложено подтвердить установку исправлений.

7. Проверьте обновление с помощью командлета `Get-HcsUpdateStatus`.

    Ниже приведен пример выходных данных для обновления, которое выполняется. Пока обновление выполняется, `RunInprogress` будет иметь значение `True`.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 9/02/2015 10:36:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :
        ````

     Ниже приведен пример выходных данных для обновления, установка которого завершена. После установки обновления `RunInProgress` будет иметь значение `False`.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 9/02/2015 10:56:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````

	> [AZURE.NOTE] В некоторых случаях командлет выдает значение `False`, пока обновление еще устанавливается. Чтобы проверить установку обновления, подождите несколько минут, выполните эту команду еще раз и убедитесь в том, что `RunInProgress` имеет значение `False`. Если это так, значит, исправление установлено.

8. Когда установка обновления будет завершена, проверьте версии программного обеспечения системы. Введите следующую команду:

    `Get-HcsSystem`

    Номера версий должны быть следующими:

    - HcsSoftwareVersion: 6.3.9600.17584
    - CisAgentVersion: 1.0.9049.0
    - MdsAgentVersion: 26.0.4696.1433

	Если после установки обновления номера версий не изменились, значит, исправление установить не удалось. В этом случае обратитесь за дополнительной помощью в [службу поддержки Майкрософт](storsimple-contact-microsoft-support.md).

9. Повторите шаги 3–5, чтобы установить оставшееся обычное исправление (KB3043005).

#### Установка и проверка исправлений режима обслуживания

Используйте KB3063416, чтобы установить обновления встроенного ПО дисков. Эти обновления прерывают работу, и на их завершение может потребоваться около 30–45 минут. Данные обновления можно установить в период планового техобслуживания, подключившись к последовательной консоли устройства.

Чтобы установить обновления встроенного ПО диска, следуйте приведенным ниже инструкциям.

1. Переведите устройство в режим обслуживания. Обратите внимание, что не следует использовать удаленное взаимодействие Windows PowerShell при переключении устройства в режим обслуживания. При подключении через последовательную консоль устройства необходимо будет выполнить этот командлет на контроллере устройства. Тип:

    `Enter-HcsMaintenanceMode`

	Результат выполнения команды показан ниже.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update1-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17584
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------
		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	После этого оба контроллера перезапускаются и переходят в режим обслуживания.

3. Чтобы установить обновление встроенного ПО диска, введите:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Результат выполнения команды показан ниже.

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
    	Enter Password:
    	WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
    	Confirm
    	This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
    	[Y] Yes [N] No (Default is "Y"): Y
    	WARNING: Installation is currently in progress. This operation can take several minutes to complete.

1.  Отслеживайте ход выполнения установки с помощью команды `Get-HcsUpdateStatus`. Обновление завершится, когда `RunInProgress` сменится на `False`.

2.  После завершения установки будет перезагружен контроллер, на котором установлено исправление режима обслуживания. В качестве варианта 1 войдите в систему с полным доступом и проверьте версию встроенного ПО диска. Тип:

	`Get-HcsFirmwareVersion`

    Ожидаемые версии встроенного ПО диска:

    `XMGG, XGEE, KZ50, F6C2, VR08`

    Выполните команду `Get-HcsFirmwareVersion` на втором контроллере, чтобы проверить обновление версии программного обеспечения. Затем можно выйти из режима обслуживания. Введите следующую команду для каждого контроллера устройства:

    `Exit-HcsMaintenanceMode`

1. При выходе из режима обслуживания контроллеры перезапускаются. Когда обновления встроенного ПО диска будут успешно установлены, а устройство будет выведено из режима обслуживания, вернитесь в классический портал Azure. Обратите внимание, что для отображения установленных обновлений режима обслуживания на портале может потребоваться до 24 часов.

<!---HONumber=AcomDC_0323_2016-->