<properties 
	pageTitle="Тестовая среда бизнес-приложений | Microsoft Azure" 
	description="Научитесь создавать веб бизнес-приложения в гибридной облачной среде для ИТ-специалистов или тестирования разработки." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Настройка веб бизнес-приложения в гибридном облаке для тестирования

В этом разделе описываются шаги по созданию гибридной облачной среды для тестирования бизнес-приложения для Интернета, размещенного в Microsoft Azure. Это конфигурация, которая получается в результате.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Пример рабочего бизнес-приложения, размещенного в Azure, можно найти в схеме архитектурного проекта **Бизнес-приложения** в разделе [Схемы и проекты архитектуры программного обеспечения Майкрософт](http://msdn.microsoft.com/dn630664).

Данная конфигурация имитирует бизнес-приложение в рабочей среде Azure из вашего расположения в Интернете. В ее состав входит:

- упрощенная локальная сеть (подсеть Corpnet);
- виртуальная сеть с подключением между организациями, размещенная в Azure (TestVNET);
- VPN-подключение типа "сеть-сеть";
- сервер бизнес-приложения, SQL Server и дополнительный контроллер домена в виртуальной сети TestVNET.

Это основа и общая начальная точка, на базе которой можно:

- разрабатывать и тестировать бизнес-приложения, размещенные в службах IIS, с помощью сервера базы данных SQL Server 2014 в Azure.
- выполнять тестирование этой гибридной облачной рабочей нагрузки ИТ-среды.

Существует три основных этапа настройки данной тестовой среды гибридного облака:

1.	настройка гибридной облачной среды для тестирования.
2.	настройка компьютера с SQL Server (SQL1).
3.	настройка сервера с бизнес-приложением (LOB1).

Для этой рабочей нагрузки требуется подписка Azure. Если у вас есть подписка MSDN или Visual Studio, см. раздел [Ежемесячная сумма денег на счете в Azure для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Этап 1. Настройка гибридной облачной среды

Следуйте указаниям в статье [Настройка гибридной облачной среды для тестирования](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md). Поскольку для данной тестовой среды не нужен сервер APP1 в подсети Corpnet, можно его спокойно выключить на время.

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)

> [AZURE.NOTE] На этапе 1 можно также настроить [смоделированную гибридную облачную среду для тестирования](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md).
 
## Этап 2. Настройка компьютера с SQL Server (SQL1)

На портале Azure запустите компьютер DC2, если это необходимо.

Теперь создайте виртуальную машину Azure для SQL1, выполнив следующие команды в командной строке Azure PowerShell на локальном компьютере. Перед выполнение этих команд заполните значения переменных и удалите символы < and >.

	$rgName="<your resource group name>"
	$locName="<the Azure location of your resource group>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Используйте портал Azure, чтобы подключиться к SQL1 с использованием учетной записи локального администратора.

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
	ping dc2.corp.contoso.com

В результате выполнения команды ping должны возвратиться четыре успешных ответа с IP-адреса 192.168.0.4.

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

Затем присоедините виртуальную машину SQL1 к домену Active Directory CORP, выполнив следующие команды в командной строке Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Используйте учетную запись CORP\\User1 при появлении запроса на ввод учетных данных домена для команды **Add-Computer**.

После перезапуска используйте портал Azure, чтобы подключиться к SQL1 с использованием *учетной записи локального администратора*.

Далее настройте SQL Server 2014 для использования диска F: для новых баз данных и для разрешений учетной записи пользователя.

1.	На начальном экране введите **SQL Server Management**, а затем щелкните **SQL Server 2014 Management Studio**.
2.	В окне **Подключение к серверу** щелкните **Подключить**.
3.	В древовидном представлении обозревателя объектов щелкните правой кнопкой мыши **SQL1**, а затем щелкните **Свойства**.
4.	В окне **Свойства сервера** щелкните **Параметры базы данных**.
5.	Найдите **Места хранения, используемые базой данных по умолчанию** и задайте следующие значения: 
	- Для элемента **Данные** введите путь **f:\\Data**.
	- Для элемента **Журнал** введите путь **f:\\Log**.
	- Для элемента **Резервное копирование** введите путь **f:\\Backup**.
	- Примечание. Только новые базы данных будут использовать эти расположения.
6.	Нажмите кнопку **ОК**, чтобы закрыть окно.
7.	В древовидном представлении **обозревателя объектов** откройте **Безопасность**.
8.	Щелкните правой кнопкой мыши **Имена входа**, а затем выберите **Создать имя входа**.
9.	В поле **Имя входа** введите **CORP\\User1**.
10.	На странице **Роли сервера** щелкните **sysadmin**, а затем нажмите кнопку **ОК**.
11.	Закройте Microsoft SQL Server Management Studio.

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## Этап 3. Настройка сервера с бизнес-приложением (LOB1)

Сначала создайте виртуальную машину Azure для LOB1, выполнив следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Затем используйте портал Azure, чтобы подключиться к LOB1 с помощью учетных данных локального администратора.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения. Из командной строки Windows PowerShell с правами администратора на LOB1 выполните следующие команды.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

В результате выполнения команды ping должны возвратиться четыре успешных ответа с IP-адреса 192.168.0.4.

Затем присоедините виртуальную машину LOB1 к домену Active Directory CORP, выполнив следующие команды в командной строке Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Используйте учетную запись CORP\\User1 при появлении запроса на ввод учетных данных домена для команды **Add-Computer**.

После перезапуска используйте портал Azure для подключения к LOB1 с помощью учетной записи CORP\\User1 и ее пароля.

Затем настройте LOB1 для служб IIS и протестируйте доступ из CLIENT1.

1.	Запустите диспетчер сервера и нажмите кнопку **Добавить роли и компоненты**.
2.	На странице «Перед началом работы» щелкните **Далее**.
3.	На странице выбора типа установки нажмите кнопку **Далее**.
4.	На странице "Выбор целевого сервера" щелкните **Далее**.
5.	На странице «Роли сервера» в списке **Роли** щелкните **Веб-сервер (IIS)**.
6.	При появлении запроса нажмите кнопку **Добавить компоненты**, а затем щелкните **Далее**.
7.	На странице "Выбор компонентов" щелкните **Далее**.
8.	На странице "Веб-сервер (IIS)" щелкните **Далее**.
9.	На странице "Выбор служб ролей" установите или снимите флажки для служб, которые необходимы для тестирования бизнес-приложения, а затем щелкните **Далее**.
10.	На странице "Подтверждение выбранных элементов для установки" щелкните **Установить**.
11.	Дождитесь завершения установки компонентов и нажмите кнопку **Закрыть**.
12.	Войдите на компьютер CLIENT1 с учетными данными учетной записи CORP\\User1, а затем запустите Internet Explorer.
13.	В адресной строке введите **http://lob1/** и нажмите клавишу ВВОД. Вы увидите веб-страницу по умолчанию IIS 8.

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
Данная среда теперь готова для развертывания веб-приложения на LOB1, а также тестирования функциональности и производительности из подсети Corpnet.

## Дальнейшие действия

- Развертывание [рабочей среды](virtual-machines-windows-lob-overview.md).

<!---HONumber=AcomDC_0601_2016-->