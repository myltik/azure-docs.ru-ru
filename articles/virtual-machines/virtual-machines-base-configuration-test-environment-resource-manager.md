<properties
	pageTitle="Создание тестовой среды с базовой конфигурацией с помощью Azure Resource Manager"
	description="Узнайте, как с помощью диспетчера ресурсов создать простую тестовую среду или среду разработки, которая моделирует упрощенную интрасеть в Microsoft Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="josephd"/>

# Создание тестовой среды с базовой конфигурацией с помощью Azure Resource Manager

В этой статье содержатся пошаговые указания по созданию тестовой среды с базовой конфигурацией в виртуальной сети Microsoft Azure с использованием виртуальных машин, созданных в диспетчере ресурсов.

Полученную тестовую среду можно использовать:

- для разработки и тестирования приложений;
- в качестве начальной конфигурации персонализированной расширенной тестовой среды, которая включает дополнительные виртуальные машины и службы Azure.

Тестовая среда с базовой конфигурацией состоит из подсети Corpnet в сугубо облачной виртуальной, которая называется TestLab. Эта подсеть моделирует упрощенную частную интрасеть, подключенную к Интернету.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

Она содержит следующие виртуальные машины:

- виртуальную машину Azure под управлением Windows Server 2012 R2 с именем DC1, которая настроена в качестве контроллера домена интрасети и сервера доменных имен (DNS-сервера);
- виртуальную машину Azure под управлением Windows Server 2012 R2 с именем APP1, которая настроена в качестве виртуальной машины общего применения и веб-сервера;
- виртуальную машину Azure под управлением Windows Server 2012 R2, которая называется CLIENT1. Эта машина работает в качестве клиента интрасети.

Такая конфигурация DC1, APP1, CLIENT1 и дополнительных компьютеров подсети Corpnet позволяет:

- подключить их к Интернету, чтобы устанавливать обновления, обеспечить доступ к интернет-ресурсам в режиме реального времени и использовать общедоступные облачные технологии, такие как Microsoft Office 365 и другие службы Azure;
- управлять ими удаленно, используя подключения к удаленному рабочему столу, с помощью компьютера, подключенного к Интернету или сети организации.

Настройка подсети Corpnet для тестовой среды с базовой конфигурацией Windows Server 2012 R2 в Azure предусматривает четыре этапа.

1.	Создание виртуальной сети.
2.	Настройка DC1.
3.	Настройка APP1.
4.	Настройка CLIENT1.

Если у вас еще нет учетной записи Azure, вы можете зарегистрироваться на странице [Пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/), чтобы получить бесплатную пробную версию. При наличии подписки MSDN см. [Преимущества Azure для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [AZURE.NOTE]Работа виртуальных машин в Azure влечет за собой текущие затраты. На эти затраты выставляется счет при использовании бесплатной пробной версии, подписки MSDN или платной подписки. Дополнительную информацию о затратах на работу виртуальных машин Azure см. в разделах [Цены на виртуальные машины](http://azure.microsoft.com/pricing/details/virtual-machines/) и [Калькулятор стоимости — оцените свои затраты](http://azure.microsoft.com/pricing/calculator/). Чтобы узнать, как снизить затраты, см. подраздел [Снижение затрат на виртуальные машины тестовой среды в Azure](#costs).

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Тестовая среда с базовой конфигурацией](virtual-machines-base-configuration-test-environment.md)


## Этап 1. Создание виртуальной сети

Сначала при необходимости установите Azure PowerShell на локальном компьютере, следуя указания в статье [Установка и настройка Azure PowerShell](../install-configure-powershell.md). Откройте командную строку Azure PowerShell.

Затем выберите правильную подписку Azure, выполнив следующие команды: Замените все содержимое внутри кавычек, включая символы < and >, правильным именем.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Правильное имя подписки можно узнать в свойстве SubscriptionName в выходных данных команды **Get-AzureSubscription**.

Перейдите в режим диспетчера ресурсов Azure PowerShell.

	Switch-AzureMode AzureResourceManager 

Создайте группу ресурсов для лаборатории тестирования с базовой конфигурацией. Чтобы вывести список существующих групп ресурсов и выбрать уникальное имя для новой группы, используйте эту команду.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Следующие команды помогут получить список расположений Azure, в которых можно создать виртуальные машины на основе диспетчера ресурсов.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Создайте группу ресурсов, выполнив следующие команды. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Для виртуальных машин на основе диспетчера ресурсов требуется учетная запись хранения на основе диспетчера ресурсов. Необходимо выбрать для нее глобально уникальное имя, содержащее только строчные буквы и цифры. Чтобы вывести список существующих учетных записей хранения, используйте эту команду.

	Get-AzureStorageAccount | Sort Name | Select Name

Чтобы убедиться, что выбранное имя учетной записи хранения глобально уникально, запустите команду **Test-AzureName** в режиме управления службами Azure PowerShell. Используйте следующие команды.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Если в результате выполнения команды Test-AzureName отображается значение **False**, предложенное имя является уникальным. После выбора уникального имени вернитесь в режим диспетчера ресурсов Azure PowerShell, используя эту команду.

	Switch-AzureMode AzureResourceManager 

Создайте для новой тестовой среды учетную запись хранения, выполнив следующие команды.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<storage account name>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Затем необходимо создать виртуальную сеть Azure TestLab, в которой будет размещена подсеть базовой конфигурации Corpnet.

	$rgName="<name of your new resource group>"
	$locName="<Azure location name, such as West US>"
	$corpnetSubnet=New-AzureVirtualNetworkSubnetConfig -Name Corpnet -AddressPrefix 10.0.0.0/24
	New-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/8 -Subnet $corpnetSubnet –DNSServer 10.0.0.4

Это текущая конфигурация.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG01.png)

## Этап 2. Настройка DC1

DC1 — это контроллер домена для доменных служб Active Directory (AD DS) corp.contoso.com и DNS-сервер для виртуальных машин в виртуальной сети TestLab.

Чтобы создать виртуальную машину Azure для DC1, сначала введите имена своей группы ресурсов и учетной записи хранения, а затем выполните следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 10.0.0.4
	$vm=New-AzureVMConfig -VMName DC1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName DC1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name DC1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Затем подключитесь к виртуальной машине DC1.

1.	В левой области портала предварительной версии Azure щелкните **Просмотреть все**, выберите пункт **Виртуальные машины** в списке **Обзор**, а затем щелкните виртуальную машину **DC1**.  
2.	В области **DC1** щелкните **Подключить**.
3.	При появлении запроса откройте скачанный файл DC1.rdp.
4.	При появлении запроса в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Подключиться**.
5.	При появлении запроса используйте следующие учетные данные:
- Имя: **DC1\\**[имя учетной записи локального администратора]
- Пароль: [пароль учетной записи локального администратора]
6.	После появления запроса, ссылающегося на сертификаты, в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Да**.

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

Теперь настройте DC1 в качестве контроллера домена и DNS-сервера для домена corp.contoso.com. Выполните следующие команды в командной строке Windows PowerShell с правами администратора.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

После перезапуска DC1 подключитесь к виртуальной машине DC1 повторно.

1.	В левой области портала предварительной версии Azure щелкните "Просмотреть все", выберите пункт "Виртуальные машины" в списке "Обзор", а затем щелкните виртуальную машину DC1.
2.	В области DC1 щелкните "Подключить".
3.	При появлении запроса на открытие DC1.rdp щелкните **Открыть**.
4.	При появлении запроса в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Подключиться**.
5.	При появлении запроса используйте следующие учетные данные:
- Имя: **CORP\\**[имя учетной записи локального администратора]
- Пароль: [пароль учетной записи локального администратора]
6.	После появления запроса, ссылающегося на сертификаты, в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Да**.

Теперь в Active Directory создайте учетную запись пользователя, которая будет использоваться при входе на компьютер-член домена CORP. Выполните по одной следующие команды в командной строке Windows PowerShell с правами администратора.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Обратите внимание, что после выполнения первой команды вы получите запрос на ввод пароля учетной записи User1. Выберите надежный пароль, так как эта учетная запись будет использоваться для подключения к удаленному рабочему столу на всех компьютерах-членах домена CORP. Чтобы проверить его надежность, см. раздел [Проверка надежности пароля. Использование надежных паролей](https://www.microsoft.com/security/pc-security/password-checker.aspx). Запишите пароль учетной записи User1 и сохраните его в надежном месте.

Закройте сеанс удаленного рабочего стола с DC1 и снова подключитесь, используя учетную запись CORP\\User1.

Теперь, чтобы обеспечить трафик для программы Ping, выполните следующую команду в командной строке Windows PowerShell с правами администратора.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Это текущая конфигурация.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG02.png)

## Этап 3. Настройка APP1

APP1 предоставляет веб-службы общего доступа и службы обмена файлами.

Чтобы создать виртуальную машину Azure для APP1, сначала введите имена своей группы ресурсов и учетной записи хранения, а затем выполните следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName APP1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for APP1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName APP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/APP1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name APP1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Затем подключитесь к виртуальной машине APP1, используя имя пользователя и пароль учетной записи локального администратора APP1, после чего откройте командную строку Windows PowerShell с правами администратора.

Чтобы проверить разрешение имен и сетевое подключение между APP1 и DC1, выполните команду **ping dc1.corp.contoso.com** и убедитесь, что возвращено четыре ответа.

Затем присоедините виртуальную машину APP1 к домену CORP, выполнив следующие команды в командной строке Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Обратите внимание, что после ввода команды Add-Computer необходимо предоставить учетные данные учетной записи домена CORP\\User1.

После перезапуска виртуальной машины APP1 подключитесь к ней, используя имя пользователя и пароль учетной записи CORP\\User1, а затем откройте командную строку Windows PowerShell с правами администратора.

Далее настройте APP1 в качестве веб-сервера, выполнив следующую команду в командной строке Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Теперь создайте общую папку и текстовый файл в этой папке в APP1, используя следующие команды.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Это текущая конфигурация.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG03.png)

## Этап 4. Настройка CLIENT1

CLIENT1 работает в интрасети Contoso как обычный ноутбук, планшет или настольный компьютер.

Чтобы создать виртуальную машину Azure для CLIENT1, сначала введите имена своей группы ресурсов и учетной записи хранения, а затем выполните следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName CLIENT1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for CLIENT1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName CLIENT1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id	
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/CLIENT1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name CLIENT1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Затем подключитесь к виртуальной машине CLIENT1, используя имя пользователя и пароль учетной записи локального администратора CLIENT1, после чего откройте командную строку Windows PowerShell с правами администратора.

Чтобы проверить разрешение имен и сетевое подключение между CLIENT1 и DC1, выполните команду **ping dc1.corp.contoso.com** в командной строке Windows PowerShell и убедитесь, что возвращено четыре ответа.

Затем присоедините виртуальную машину CLIENT1 к домену CORP, выполнив следующие команды в командной строке Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Обратите внимание, что после ввода команды Add-Computer необходимо предоставить учетные данные учетной записи домена CORP\\User1.

После перезапуска виртуальной машины CLIENT1 подключитесь к ней, используя имя пользователя и пароль учетной записи CORP\\User1, а затем откройте командную строку Windows PowerShell с правами администратора.

Теперь убедитесь, что из CLIENT1 можно получить доступ к общим веб- и файловым ресурсам в APP1.

1.	В диспетчере серверов в древовидном представлении щелкните **Локальный сервер**.
2.	В **Свойства для CLIENT1** щелкните **Включено** возле **Конфигурация усиленной безопасности Internet Explorer**.
3.	В **Конфигурация усиленной безопасности Internet Explorer** щелкните **Выключено** для **Администраторы** и **Пользователи** и нажмите кнопку **OK**.
4.	На начальном экране щелкните **Internet Explorer**, а затем нажмите кнопку **OK**.
5.	В адресной строке введите **http://app1.corp.contoso.com/** и нажмите клавишу ВВОД. Вы должны увидеть веб-страницу по умолчанию «Службы IIS» для APP1.6.	На панели задач рабочего стола щелкните значок «Проводник».
7.	В адресной строке введите **\\\\app1\\Files** и нажмите клавишу ВВОД.
8.	Вы должны увидеть окно папки с содержимым, которое находится в общей папке «Файлы».
9.	В окне общей папки **Файлы** дважды щелкните файл **Example.txt**. Вы увидите содержимое этого файла.
10.	Закройте **example.txt — Блокнот** и окна общей папки **Файлы**.

Это последнее изменение конфигурации.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

Теперь базовая конфигурация в Azure готова к разработке и тестированию приложений и дополнительных тестовых сред.

## Дополнительные ресурсы

[Гибридные облачные тестовые среды](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Тестовая среда с базовой конфигурацией](virtual-machines-base-configuration-test-environment.md)


## <a id="costs"></a>Снижение затрат на виртуальные машины тестовой среды в Azure.

Чтобы уменьшить затраты на работу виртуальных машин в тестовой среде, можно использовать один из следующих способов.

- Создайте тестовую среду и выполните необходимые тестирования и демонстрации как можно быстрее. По завершении удалите виртуальные машины тестовой среды.
- Завершите работу виртуальных машин в тестовой среде так, чтобы они перешли в освобожденное состояние.

Чтобы завершить работу виртуальных машин с помощью Azure PowerShell, введите имя группы ресурсов и выполните следующие команды.

	$rgName="<your resource group name>"
	Stop-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "DC1" -Force -StayProvisioned

Чтобы при запуске виртуальные машины, которые пребывают в состоянии «Остановлена» («Освобождена»), работали надлежащим образом, эти машины следует запустить в следующем порядке:

1.	DC1
2.	APP1
3.	CLIENT1

Чтобы запустить виртуальные машины с помощью Azure PowerShell, введите имя группы ресурсов и выполните следующие команды.

	$rgName="<your resource group name>"
	Start-AzureVM -ResourceGroupName $rgName -Name "DC1"
	Start-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Start-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"

<!---HONumber=July15_HO5-->