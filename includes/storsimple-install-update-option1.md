
#### Установка обновления 1 из оболочки Windows PowerShell для StorSimple

1. Для загрузки обновления программного обеспечения выполните следующие действия.

    1. Запустите Internet Explorer и откройте страницу [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx).
    2. Если раньше вы не использовали этот сервис, вам будет предложено установить каталог Центра обновления Майкрософт. Нажмите **Установить**.
    
        ![Установка каталога](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. Появится экран поиска в каталоге. Введите в поле поиска значение **3063418** и щелкните **Поиск**.

        ![Поиск в каталоге](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. Вы увидите пакет **StorSimple Update 1.0 Appliance Update**. Щелкните **Добавить**. Пакет обновлений будет добавлен в вашу корзину.

        ![Пакет обновлений](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. Щелкните **Просмотреть корзину**.
 
        ![Просмотр корзины](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. Щелкните элемент **Загрузить**. Введите или выберите локальное расположение, в которое хотите загрузить файл. Обновление (all-hcsmdssoftwareupdate_288da2cc8cd2e3c3958b603a79346cb586fb8fe3.exe) будет загружено в папку StorSimple Update 1.0 Appliance Update bundle (KB3063418) в указанном месте. Этот каталог также можно скопировать в сетевую папку, которая доступна с вашего устройства.
        
2. Чтобы установить обновление для ПО, откройте интерфейс Windows PowerShell на последовательной консоли своего устройства StorSimple. Подробные инструкции см. в разделе [Использование PuTTY для подключения к последовательной консоли устройства](#use-putty-to-connect-to-the-serial-console).

3. В командной строке нажмите клавишу ВВОД.

4. Выберите **Вариант 1**, чтобы войти на устройство с правами на полный доступ.

5. Чтобы установить пакет обновления, в командной строке введите следующую команду:

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    Параметр "credential" используется только для доступа к общему ресурсу с проверкой подлинности.

    Пример выходных данных этой команды показан ниже.

        ````
        Controller0>Start-HcsHotfix -Path \10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. Введите **Y**, когда будет предложено подтвердить установку исправлений.

7. Для наблюдения за ходом установки используйте командлет Get-HcsUpdateStatus.

    Ниже приведен пример выходных данных для обновления, которое выполняется.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     Ниже приведен пример выходных данных для обновления, установка которого завершена.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
 
8. После завершения обновления откройте страницу "Обслуживание" на портале управления. Проверьте наличие обновлений. В списке должны появиться новые обновления для программного обеспечения.

9. Щелкните **Установить обновления**, чтобы применить все доступные на портале обновления программного обеспечения.

10. После установки обновлений проверьте номера версий системного ПО, драйверов и встроенного программного обеспечения. Введите следующую команду:

    `Get-HcsSystem`

    Номера версий должны быть следующими:

    - HcsSoftwareVersion: 6.3.9600.17491
    - CisAgentVersion: 1.0.9037.0
    - MdsAgentVersion: 26.0.4696.1433 
 
11. Чтобы убедиться в том, что встроенное программное обеспечение обновилось корректно, введите следующую команду:

    `Start-HcsFirmwareCheck`

    Для встроенного программного обеспечения должно отображаться состояние **UpToDate**.

<!---HONumber=62-->