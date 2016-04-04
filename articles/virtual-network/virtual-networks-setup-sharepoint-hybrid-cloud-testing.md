<properties 
	pageTitle="Тестовая среда фермы SharePoint 2013 | Microsoft Azure" 
	description="Узнайте, как создать двухуровневую ферму интрасети SharePoint Server 2013 в гибридной облачной среде для разработки или тестирования ИТ-специалистами." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/28/2016" 
	ms.author="josephd"/>

# Настройка фермы SharePoint интрасети в гибридном облаке для тестирования

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.
 

В этом разделе описываются шаги по созданию гибридной облачной среды для тестирования фермы SharePoint интрасети, размещенной в Microsoft Azure. Это конфигурация, которая получается в результате.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
Данная конфигурация имитирует SharePoint в рабочей среде Azure из вашего расположения в Интернете. В ее состав входит:

- упрощенная локальная сеть (подсеть Corpnet);
- виртуальная сеть с подключением между организациями, размещенная в Azure (TestVNET);
- VPN-подключение типа "сеть-сеть";
- двухуровневая ферма SharePoint и дополнительный контроллер домена в виртуальной сети TestVNET.

Это основа и общая начальная точка, на базе которой можно:

- разрабатывать и тестировать приложения на ферме интрасети SharePoint в гибридной облачной среде.
- выполнять тестирование этой гибридной облачной рабочей нагрузки ИТ-среды.

Существует три основных этапа настройки данной тестовой среды гибридного облака:

1.	настройка гибридной облачной среды для тестирования.
2.	настройка компьютера с SQL Server (SQL1).
3.	настройка сервера SharePoint (SP1).

Если у вас еще нет подписки Azure, вы можете зарегистрироваться для бесплатного пробного использования на странице [Попробуйте Azure](https://azure.microsoft.com/pricing/free-trial/). При наличии подписки MSDN см. [Преимущества Azure для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Этап 1. Настройка гибридной облачной среды

Следуйте указаниям в статье [Настройка гибридной облачной среды для тестирования](virtual-networks-setup-hybrid-cloud-environment-testing.md). Поскольку для данной тестовой среды не нужен сервер APP1 в подсети Corpnet, можно его спокойно выключить на время.

Это текущая конфигурация.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_1.png)

> [AZURE.NOTE] На этапе 1 можно также настроить смоделированную гибридную облачную среду для тестирования. Указания см. в статье [Настройка смоделированной гибридной облачной среды для тестирования](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).
 
## Этап 2. Настройка компьютера с SQL Server (SQL1)

Из портала управления Azure запустите компьютер DC2, если это необходимо.

Сначала создайте подключение удаленного рабочего стола к DC2, используя учетные данные CORP\\User1.

Далее создайте учетную запись администратора фермы SharePoint. Откройте командную строку Windows PowerShell с правами администратора в DC2 и выполните следующую команду.

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

При появлении запроса укажите пароль учетной записи SPFarmAdmin. Введите надежный пароль и запишите его в безопасном месте.

Теперь создайте виртуальную машину Azure для SQL1, выполнив следующие команды в командной строке Azure PowerShell на локальном компьютере.


	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for SQL1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Затем подключитесь к новой виртуальной машине SQL1 *с помощью учетной записи локального администратора*.

1.	На левой панели портала управления Azure щелкните **Виртуальные машины**, а затем щелкните **Работающие** в столбце "Состояние" для SQL1.
2.	На панели задач щелкните **Подключиться**. 
3.	При появлении запроса на открытие SQL1.rdp щелкните **Открыть**.
4.	При появлении запроса в окне сообщений "Подключение к удаленному рабочему столу" щелкните **Подключиться**.
5.	При появлении запроса используйте следующие учетные данные:
	- Имя: **SQL1\**[имя учетной записи локального администратора].
	- Пароль: [пароль учетной записи локального администратора]
6.	После появления запроса, ссылающегося на сертификаты, в окне сообщений "Подключение к удаленному рабочему столу" щелкните **Да**.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения и SQL Server. Из командной строки Windows PowerShell с правами администратора на SQL1 выполните следующие команды.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

В результате выполнения команды ping должны быть получены четыре успешных ответа с IP-адреса 10.0.0.1.

Далее следует добавить дополнительный диск данных как новый том с буквой диска F:.

1.	В левой области диспетчера серверов щелкните **Файловые службы и службы хранилища**, а затем щелкните **Диски**.
2.	В области содержимого в группе **Диски** щелкните **диск 2** (при этом для параметра **Раздел** должно быть задано значение **Неизвестный**).
3.	Щелкните **Задачи**, а затем **Новый том**.
4.	На странице «Перед началом работы» мастера создания томов щелкните **Далее**.
5.	На странице «Выбор сервера и диска» щелкните **Диск 2**, а затем щелкните **Далее**. При появлении запроса нажмите кнопку **OK**.
6.	На странице «Выбор размера тома» щелкните **Далее**.
7.	На странице «Назначение букве диска или папке» щелкните **Далее**.
8.	На странице «Выбор параметров файловой системы» щелкните **Далее**.
9.	На странице «Подтверждение выбора» щелкните **Создать**.
10.	После завершения нажмите кнопку **Закрыть**.

Выполните следующие команды из командной строки Windows PowerShell на SQL1:

	md f:\Data
	md f:\Log
	md f:\Backup

Далее настройте SQL Server 2014 для использования диска F: для новых баз данных и для разрешений учетной записи пользователя.

1.	На начальном экране введите **SQL Server Management**, а затем щелкните **SQL Server 2014 Management Studio**.
2.	В окне **Подключение к серверу** щелкните **Подключить**.
3.	В древовидном представлении обозревателя объектов щелкните правой кнопкой мыши **SQL1**, а затем щелкните **Свойства**.
4.	В окне **Свойства сервера** щелкните **Параметры базы данных**.
5.	Найдите **Места хранения, используемые базой данных по умолчанию** и задайте следующие значения: 
	- Для элемента **Данные** введите путь **f:\\Data**.
	- Для элемента **Журнал** введите путь **f:\\Log**.
	- Для элемента **Резервное копирование** введите путь **f:\\Backup**.
	- Обратите внимание, что только новые базы данных используют эти расположения.
6.	Нажмите кнопку **ОК**, чтобы закрыть окно.
7.	В древовидном представлении **обозревателя объектов** откройте **Безопасность**.
8.	Щелкните правой кнопкой мыши **Имена входа**, а затем выберите **Создать имя входа**.
9.	В поле **Имя входа** введите **CORP\\User1**.
10.	На странице **Роли сервера** щелкните **sysadmin**, а затем нажмите кнопку **ОК**.
11.	В древовидном представлении **обозревателя объектов** щелкните правой кнопкой мыши **Имена входа**, а затем щелкните **Создать имя входа**.
12.	На странице **Общие** в поле **Имя входа** введите **CORP\\SPFarmAdmin**.
13.	На странице **Роли сервера** выберите **dbcreator**, а затем нажмите кнопку **ОК**.
14.	Закройте Microsoft SQL Server Management Studio.

Это текущая конфигурация.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_2.png)

 
## Этап 3. Настройка сервера SharePoint (SP1)

Сначала создайте виртуальную машину Azure для SP1, выполнив следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for SP1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SP1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Затем подключитесь к виртуальной машине SP1 с учетными данными CORP\\User1.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения. Из командной строки Windows PowerShell с правами администратора на SP1 выполните следующие команды.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

В результате выполнения команды ping должны быть получены четыре успешных ответа с IP-адреса 10.0.0.1.

После этого настройте SP1 для новой фермы SharePoint и сайта рабочей группы по умолчанию.

1.	На начальном экране введите **Продукты SharePoint 2013**, а затем щелкните **Мастер настройки продуктов SharePoint 2013**. Когда вам будет предложено разрешить программе вносить изменения на компьютере, нажмите кнопку **Да**.
2.	На странице «Продукты SharePoint» нажмите кнопку **Далее**. 
3.	В диалоговом окне, уведомляющем о том, что некоторые службы, возможно, потребуется перезапустить во время настройки, щелкните **Да**.
4.	На странице "Подключение к ферме серверов" щелкните **Создать новую ферму серверов**, а затем щелкните **Далее**.
5.	На странице "Указание параметров базы данных конфигурации" введите **sql1.corp.contoso.com** в поле **Сервер базы данных**, **CORP\\SPFarmAdmin** — в поле **Имя пользователя**, пароль учетной записи SPFarmAdmin — в поле **Пароль**, а затем щелкните **Далее**.
6.	На странице "Задание параметров безопасности фермы" введите ****P@ssphrase** в поля **Парольная фраза** и **Подтверждение пароля**, а затем нажмите кнопку **Далее**.
7.	На странице настройки веб-приложения центра администрирования SharePoint нажмите кнопку **Далее**.
8.	На странице «Выполнение мастера настройки продуктов SharePoint» нажмите кнопку **Далее**. Завершение мастера настройки продуктов SharePoint может занять несколько минут.
9.	На странице "Настройка успешно завершена" нажмите кнопку **Готово**. После завершения Internet Explorer запускается с вкладки с именем "Мастер начальной настройки фермы".
10.	В диалоговом окне **Помочь сделать SharePoint лучше** щелкните **Нет, я не хочу участвовать** и нажмите кнопку **ОК**.
11.	Для окна **Как следует выполнить настройку фермы SharePoint?** щелкните **Запуск мастера**.
12.	На странице "Настройка фермы SharePoint" в разделе **Учетная запись службы** щелкните **Использовать существующую управляемую учетную запись**.
13.	В окне **Службы** снимите все флажки, за исключением флажка рядом с полем **Служба состояний**, а затем щелкните **Далее**. На короткое время может отображаться страница "Выполняется" до завершения.
14.	На странице "Создание семейства веб-сайтов" в разделе **Название и описание** введите **Contoso Corporation** в поле **Название**, укажите URL-адрес ****http://sp1**/, а затем нажмите кнопку **ОК**. На короткое время может отображаться страница "Выполняется" до завершения. На этом шаге создается веб-сайт группы с URL-адресом http://sp1.
15.	На странице "Выполнение мастера настройки фермы завершено" нажмите кнопку **Готово**. На вкладке Internet Explorer отображается сайт центра администрирования SharePoint 2013.
16.	Войдите на компьютер CLIENT1 с учетными данными учетной записи CORP\\User1, а затем запустите Internet Explorer.
17.	В адресной строке введите ****http://sp1/** и нажмите клавишу ВВОД. Должен появиться сайт рабочей группы в SharePoint для Contoso Corporation. Сайту может потребоваться некоторое время для подготовки к просмотру.

Это текущая конфигурация.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
Ферма SharePoint интрасети в гибридной облачной среде теперь готова для тестирования.

## Дальнейшие действия

- Настройте [рабочую нагрузку для рабочей среды](../virtual-machines/virtual-machines-windows-sp-intranet-overview.md).

<!---HONumber=AcomDC_0323_2016-->