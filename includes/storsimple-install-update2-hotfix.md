<!--author=alkohli last changed: 03/17/16-->

#### Загрузка исправления

Для загрузки обновления программного обеспечения из каталога обновления Майкрософт выполните следующие действия.

1. Запустите Internet Explorer и откройте страницу [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Если вы впервые используете каталог Центра обновления Майкрософт на этом компьютере, нажмите кнопку **Установить**, когда будет предложено установить надстройку каталога Центра обновления Майкрософт. ![Установка каталога](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. В поле поиска каталога Центра обновления Майкрософт введите номер необходимого исправления в базе данных (KB), например **3121901**, а затем нажмите кнопку **Найти**.

    Появится список исправлений, например **Совокупное обновление программного обеспечения версии 2.0 для StorSimple серии 8000**.

    ![Поиск в каталоге](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Щелкните **Добавить**. Обновление будет добавлено в корзину.

5. Найдите дополнительные исправления, перечисленные в приведенной выше таблице (**3121900**, **3080728**, **3090322** и **3121899**), и добавьте каждое из них в корзину.

5. Щелкните **Просмотреть корзину**.

6. Щелкните элемент **Загрузить**. Введите или **Выберите** локальное расположение, в которое хотите скачать файл. Обновления загружаются в указанное место и помещаются во вложенную папку с тем же именем, что и имя обновления. Этот каталог также можно скопировать в сетевую папку, которая доступна с вашего устройства.

>   [AZURE.NOTE]
Исправления должны быть доступны с обоих контроллеров, чтобы можно было получить любые возможные сообщения об ошибках от однорангового контроллера.

#### Установка и проверка исправлений обычного режима

Выполните следующие действия для установки и проверки обычных исправлений. Если вы уже установили их с помощью портала Azure, перейдите к [установке и проверке исправлений режима обслуживания](#to-install-and-verify-maintenance-mode-hotfixes).

1. Чтобы установить исправления, откройте интерфейс Windows PowerShell на последовательной консоли своего устройства StorSimple. Подробные инструкции см. в разделе [Использование PuTTY для подключения к последовательной консоли устройства](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). В командной строке нажмите клавишу **ВВОД**.

4. Выберите **Вариант 1**, чтобы войти на устройство с правами на полный доступ.

5. Чтобы установить исправление, в командной строке введите следующую команду:

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
        LastHotfixTimestamp : 12/21/2015 10:36:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :
        ````

     Ниже приведен пример выходных данных для обновления, установка которого завершена. После установки обновления `RunInProgress` будет иметь значение `False`.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 12/21/2015 10:59:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````

	> [AZURE.NOTE] В некоторых случаях командлет выдает значение `False`, пока обновление еще устанавливается. Чтобы проверить установку обновления, подождите несколько минут, выполните эту команду еще раз и убедитесь в том, что `RunInProgress` имеет значение `False`. Если это так, значит, исправление установлено.

8. После завершения обновления программного обеспечения повторите шаги 3–5 для установки агента SaaS и агента MDS и наблюдения за ними. Убедитесь, что `all-hcsmdssoftwareupdate_0b438ddf0d5b686aada2378b754fac8c7f2160e9.exe` установлен до `all-cismdsagentupdatebundle_f98e62f4d56c79e2a6644d027af7a2393a93827a.exe`.

9. Проверьте версии программного обеспечения системы. Тип:

    `Get-HcsSystem`

    Номера версий должны быть следующими:

    - HcsSoftwareVersion: 6.3.9600.17673
    - CisAgentVersion: 1.0.9150.0
    - MdsAgentVersion: 30.0.4698.13

	Если после установки обновления номера версий не изменились, значит, исправление установить не удалось. В этом случае обратитесь за дополнительной помощью в [службу поддержки Майкрософт](storsimple-contact-microsoft-support.md).

9. Повторите шаги 3–5 для установки оставшихся обычных исправлений.

	- Драйвер LSI — KB3121900
	- Обновление Storport — KB3080728
	- Обновление Spaceport — KB3090322

#### Установка и проверка исправлений режима обслуживания

Используйте KB3121899, чтобы установить обновления встроенного ПО дисков. Эти обновления прерывают работу, и на их завершение может потребоваться около 30 минут. Данные обновления можно установить в период планового техобслуживания, подключившись к последовательной консоли устройства.

Обратите внимание, что если встроенное ПО диска уже обновлено, устанавливать эти обновления не требуется. Запустите командлет `Get-HcsUpdateAvailability` из последовательной консоли устройства, чтобы проверить, доступны ли обновления и являются ли они критическими (обновления режима обслуживания) или некритическими (обычными).

Чтобы установить обновления встроенного ПО диска, следуйте приведенным ниже инструкциям.

1. Переведите устройство в режим обслуживания. Обратите внимание, что не следует использовать удаленное взаимодействие Windows PowerShell при переключении устройства в режим обслуживания. Вместо этого запустите следующий командлет при подключении через последовательную консоль устройства. Введите:

	`Enter-HcsMaintenanceMode`

	Результат выполнения команды показан ниже.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update2-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17664
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------

		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	После этого оба контроллера перезапускаются в режим обслуживания.

3. Чтобы установить обновление встроенного ПО диска, введите:

	`Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

	Результат выполнения команды показан ниже.

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
		Enter Password:
		WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
		Confirm
		This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
		[Y] Yes [N] No (Default is "Y"): Y
		WARNING: Installation is currently in progress. This operation can take several minutes to complete.

1.  Отслеживайте ход установки с помощью команды `Get-HcsUpdateStatus`. Обновление завершается, когда `RunInProgress` изменится на `False`.

2.  После завершения установки контроллер, на котором установлено исправление режима обслуживания, будет перезагружен. В качестве варианта 1 войдите в систему с полным доступом и проверьте версию встроенного ПО диска. Тип:

	`Get-HcsFirmwareVersion`

	Ожидаемые версии встроенного ПО диска:

	`XMGG, XGEG, KZ50, F6C2, VR08`

	Результат выполнения команды показан ниже.

        -----------------------MAINTENANCE MODE------------------------
    	Microsoft Azure StorSimple Appliance Model 8100
    	Name: Update2-8100-SHG0997879L76YD
    	Software Version: 6.3.9600.17664
    	Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    	You are connected to Controller1
    	---------------------------------------------------------------

    	Controller1>Get-HcsFirmwareVersion

    	Controller0 : TalladegaFirmware
    	  ActiveBIOS:0.45.0006
    	  BackupBIOS:0.45.0008
    	  MainCPLD:17.0.0005
    	  ActiveBMCRoot:2.0.000E
    	  BackupBMCRoot:2.0.000E
    	  BMCBoot:2.0.0001
    	  LsiFirmware:19.00.00.00
    	  LsiBios:07.37.00.00
    	  Battery1Firmware:06.29
    	  Battery2Firmware:06.29
    	  DomFirmware:X231600
    	  CanisterFirmware:3.5.0.32
    	  CanisterBootloader:5.03
    	  CanisterConfigCRC:0xD1B030A4
    	  CanisterVPDStructure:0x06
    	  CanisterGEMCPLD:0x17
    	  CanisterVPDCRC:0xEE3504B4
    	  MidplaneVPDStructure:0x0C
    	  MidplaneVPDCRC:0xA6BD4F64
    	  MidplaneCPLD:0x10
    	  PCM1Firmware:1.00|1.05
    	  PCM1VPDStructure:0x05
    	  PCM1VPDCRC:0x41BEF99C
    	  PCM2Firmware:1.00|1.05
    	  PCM2VPDStructure:0x05
    	  PCM2VPDCRC:0x41BEF99C

    	  DisksFirmware
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG

	 Запустите команду `Get-HcsFirmwareVersion` на втором контроллере, чтобы проверить, что версия программного обеспечения обновлена. Затем можно выйти из режима обслуживания. Для этого введите следующую команду для каждого контроллера устройства:

    `Exit-HcsMaintenanceMode`

1. При выходе из режима обслуживания контроллеры перезапускаются. Когда обновления встроенного ПО диска будут успешно установлены, а устройство будет выведено из режима обслуживания, вернитесь в классический портал Azure. Обратите внимание, что для отображения установленных обновлений режима обслуживания на портале может потребоваться до 24 часов.

<!---HONumber=AcomDC_0323_2016-->