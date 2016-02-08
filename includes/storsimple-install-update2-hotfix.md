<!--author=alkohli last changed: 01/26/16-->

#### Загрузка исправления

Для загрузки обновления программного обеспечения из каталога обновления Майкрософт выполните следующие действия.

1. Запустите Internet Explorer и откройте страницу [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).

2. Если раньше вы не использовали этот сервис, вам будет предложено установить каталог Центра обновления Майкрософт. Нажмите **Установить**.
    
   	![Установка каталога](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Появится экран поиска в каталоге. Введите в поле поиска значение **3121901** и щелкните **Поиск**.

    ![Поиск в каталоге](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Вы увидите **Совокупное обновление пакета программного обеспечения 2.0 для хранилища StorSimple серии 8000**. Щелкните **Добавить**. Пакет обновлений будет добавлен в вашу корзину.

5. Щелкните **Просмотреть корзину**.
 
6. Щелкните элемент **Загрузить**. Введите или **выберите** локальное расположение, в которое хотите скачать файл. Обновление будет загружено в папку (совпадает с названием обновления) в выбранном расположении. Этот каталог также можно скопировать в сетевую папку, которая доступна с вашего устройства.
       
	> [AZURE.NOTE] 
	> 
	> - Также необходимо будет загрузить **обновление драйвера LSI** (обновление контроллера SAS 2.0 для серии StorSimple 8000 — KB3121900), **обновление Storport** (исправление для Windows Server 2012 R2, 64-разрядный выпуск — KB3080728), **обновление Spaceport** (исправление для Windows Server 2012 R2, 64-разрядный выпуск — KB3090322) и **обновление встроенного ПО диска** (совокупное обновление встроенного ПО диска 2.0 для серии StorSimple 8000 — KB3121899) и скопировать в ту же общую папку.
	> - Исправление должно быть доступно с обоих контроллеров, что позволит обнаружить любые возможные сообщения об ошибках с однорангового контроллера.

#### Установка и проверка исправлений обычного режима

Выполните следующие действия для установки и проверки обычных исправлений.

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
	
8. После завершения обновления программного обеспечения повторите шаги 3–5 для установки и наблюдения за агентом SaaS и агентом MDS с помощью `CisMdsAgentUpdateBundle.exe`. Убедитесь, что `HcsMdsSoftwareUpdate.exe` установлен до `CisMdsAgentUpdateBundle.exe`.

9. Проверьте версии программного обеспечения системы. Тип:

    `Get-HcsSystem`

    Номера версий должны быть следующими:

    - HcsSoftwareVersion: 6.3.9600.17673
    - CisAgentVersion: 1.0.9150.0
    - MdsAgentVersion: 30.0.4698.13 
    
	Если после установки обновления номера версий не изменились, значит, исправление установить не удалось. В этом случае обратитесь за дополнительной помощью в [службу поддержки Майкрософт](storsimple-contact-microsoft-support.md).
    
9. Повторите шаги 3–5 для установки и мониторинга оставшихся обычных исправлений.

	- Драйвер LSI с помощью пакета `HcsLsiUpdate.exe` (KB3121900).
	- Исправление Storport с помощью пакета `Storport-KB3080728-x64.msu` (KB3080728).
	- Исправление Spaceport с помощью пакета `spaceport-KB3090322-x64.msu` (KB3090322).

#### Установка и проверка исправления режима обслуживания

Используйте пакет `DiskFirmwarePackage.exe` (KB3121899), чтобы установить обновления встроенного ПО дисков. Эти обновления прерывают работу, и на их завершение может потребоваться около 30 минут. Данные обновления можно установить в период планового техобслуживания, подключившись к последовательной консоли устройства.

Обратите внимание, что если встроенное ПО диска уже обновлено, устанавливать эти обновления не требуется. Выполните командлет `Get-HcsUpdateAvailability` из последовательной консоли устройства. Вы получите уведомление о том, что обновления доступны, и о том, являются они критическими (обновления режима обслуживания) или некритическими (обычными).
 
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

	Оба контроллера будут перезагружены. После завершения перезагрузки оба контроллера будут в режиме обслуживания.

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
	

1.  Отслеживайте ход выполнения установки с помощью команды `Get-HcsUpdateStatus`. Обновление завершается, когда `RunInProgress` меняется на `False`.
 
2.  После завершения установки будет перезагружен контроллер, на котором установлено исправление режима обслуживания. В качестве варианта 1 войдите в систему с полным доступом и проверьте версию встроенного ПО диска. Тип:
	
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

	 Запустите команду `Get-HcsFirmwareVersion` на втором контроллере, чтобы проверить обновление версии программного обеспечения. Затем можно выйти из режима обслуживания. Введите следующую команду для каждого контроллера устройства:

    `Exit-HcsMaintenanceMode`
     
1. Контроллеры будут перезагружены после выхода из режима обслуживания. Когда обновления встроенного ПО диска будут успешно установлены, а устройство будет выведено из режима обслуживания, вернитесь в классический портал Azure. Обновления режима обслуживания не устанавливаются на портале до истечения 24-часового периода.






 
 

<!---HONumber=AcomDC_0128_2016-->