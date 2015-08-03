<properties 
	pageTitle="Тестовая среда с базовой конфигурацией" 
	description="Узнайте, как создать простую тестовую среду или среду разработки, которая моделирует упрощенную интрасеть в Microsoft Azure." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="josephd"/>

# Тестовая среда с базовой конфигурацией

В этой статье содержатся пошаговые указания по созданию тестовой среды с базовой конфигурацией в виртуальной сети Microsoft Azure с использованием виртуальных машин, созданных посредством компонента управления службами.

Полученную тестовую среду можно использовать:

- для разработки и тестирования приложений;
- [для моделирования гибридной облачной среды](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md);
- для создания персонализированной тестовой среды путем расширения возможностей полученной среды за счет дополнительных виртуальных машин и служб Azure.
 
Тестовая среда с базовой конфигурацией включает в себя подсеть Corpnet в сугубо облачной виртуальной сети Azure, которая называется TestLab. Эта сеть моделирует упрощенную частную интрасеть, подключенную к Интернету.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Она содержит следующие виртуальные машины:

- Одну виртуальную машину Azure под управлением Windows Server 2012 R2, которая называется DC1. Она настроена в качестве контроллера домена интрасети и сервера доменных имен (DNS-сервера).
- Одну виртуальную машину Azure под управлением Windows Server 2012 R2, которая называется APP1. Она настроена в качестве веб-сервера и виртуальной машины общего применения.
- Одну виртуальную машину Azure под управлением Windows Server 2012 R2, которая называется CLIENT1. Эта машина будет работать в качестве клиента интрасети.

Такая конфигурация DC1, APP1, CLIENT1 и дополнительных компьютеров подсети Corpnet позволяет:

- подключить их к Интернету, чтобы устанавливать обновления, обеспечить доступ к интернет-ресурсам в режиме реального времени и использовать общедоступные облачные технологии, такие как Microsoft Office 365 и другие службы Azure; 
- управлять ими удаленно, используя подключения к удаленному рабочему столу, с помощью компьютера, подключенного к Интернету или сети организации. 

Настройка подсети Corpnet для тестовой среды с базовой конфигурацией Windows Server 2012 R2 в Azure предусматривает четыре этапа.

1.	Создание виртуальной сети Azure.
2.	Настройка DC1. 
3.	Настройка APP1. 
4.	Настройка CLIENT1.

Если у вас еще нет учетной записи Azure, вы можете зарегистрироваться на странице [Пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/), чтобы получить бесплатную пробную версию. При наличии подписки MSDN см. [Преимущества Azure для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [AZURE.NOTE]Работа виртуальных машин в Azure влечет за собой текущие затраты. На эти затраты выставляется счет при использовании бесплатной пробной версии, подписки MSDN или платной подписки. Дополнительную информацию о затратах на работу виртуальных машин Azure см. в разделах [Цены на виртуальные машины](http://azure.microsoft.com/pricing/details/virtual-machines/) и [Калькулятор стоимости — оцените свои затраты](http://azure.microsoft.com/pricing/calculator/). Чтобы узнать, как снизить затраты, см. подраздел [Снижение затрат на виртуальные машины тестовой среды в Azure](#costs).

## Этап 1. Создание виртуальной сети Azure

Сначала необходимо создать виртуальную сеть Azure TestLab, в которой будет размещена подсеть базовой конфигурации Corpnet.

1.	На портале управления Azure на панели задач последовательно выберите **Создать > Сетевые службы > Виртуальная сеть > Настраиваемое создание**.
2.	На странице «Сведения о виртуальной сети» введите **TestLab** в поле **Имя**.
3.	В поле **Расположение** выберите подходящий регион.
4.	Нажмите стрелку Далее.
5.	На странице «DNS-серверы и подключение VPN» в разделе **DNS-серверы** введите **DC1** в разделе **Выберите или введите имя**, введите **10.0.0.4** в поле **IP-адрес**, а затем щелкните стрелку «Далее».
6.	На странице «Адресное пространство виртуальной сети» в столбце **Подсети** щелкните **Subnet-1** и замените ее имя именем **Corpnet**. 
7.	В столбце **CIDR (число адресов)** подсети Corpnet щелкните **/24 (256)**.
8.	Щелкните значок Завершить. Дождитесь создания виртуальной сети прежде, чем продолжить.

Далее, чтобы установить Azure PowerShell на локальном компьютере, следуйте указаниям в разделе [Как установить и настроить Azure PowerShell](../install-configure-powershell.md). Откройте командную строку Azure PowerShell.

Сначала выберите правильную подписку Azure с помощью следующих команд. Замените все содержимое в кавычках, в том числе символы < and >, правильными именами.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Правильное имя подписки можно узнать в свойстве **SubscriptionName** в выходных данных команды **Get-AzureSubscription**.

Теперь вы можете создать облачную службу Azure. Облачная служба работает в качестве периметра безопасности и логического контейнера для виртуальных машин, размещенных в виртуальной сети. Она также позволяет устанавливать удаленное подключение к виртуальным машинам в подсети Corpnet и управлять ими.

Вам необходимо выбрать уникальное имя для облачной службы. *Имя облачной службы может содержать только буквы, цифры и дефисы. Первый и последний символ в поле должны быть буквами или цифрами.*

Например, службу можно назвать TestLab-*UniqueSequence*, где *UniqueSequence* — это сокращенное название вашей организации. К примеру, если ваша организация называется Tailspin Toys, можно назвать облачную службу TestLab-Tailspin.

Уникальность имени можно проверить, выполнив следующую команду Azure PowerShell.

	Test-AzureName -Service <Proposed cloud service name>

Если команда возвращает значение «False»,имя является уникальным. Теперь создайте облачную службу с помощью следующих команд.

	$loc="<the same location (region) as your TestLab virtual network>"
	New-AzureService -Service <Unique cloud service name> -Location $loc

Запишите фактическое имя своей облачной службы.

Далее вы можете настроить учетную запись хранения, в которой будут содержаться диски для виртуальных машин и дополнительные диски данных. *Вам необходимо выбрать уникальное имя, в котором будут содержаться только строчные буквы и цифры.* Вы можете проверить уникальность имени учетной записи хранения с помощью этой команды Azure PowerShell.

	Test-AzureName -Storage <Proposed storage account name>

Если команда возвращает значение «False»,имя является уникальным. Теперь создайте учетную запись хранения и задайте подписку, для которой она будет использоваться, с помощью следующих команд.

	$stAccount="<your storage account name>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $loc
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $stAccount

Это текущая конфигурация.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG01.png)

## Этап 2. Настройка DC1

DC1 — это контроллер домена для доменных служб Active Directory (AD DS) corp.contoso.com и DNS-сервер для виртуальных машин в виртуальной сети TestLab.

Чтобы создать виртуальную машину Azure для DC1, сначала введите имя своей облачной службы и выполните следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$serviceName="<your cloud service name>"
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC1."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC1 -InstanceSize Small -ImageName $image 
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel "AD" -LUN 0 -HostCaching None
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Затем подключитесь к виртуальной машине DC1.

1.	В левой области портала управления Azure щелкните **Виртуальные машины**, а затем щелкните **Запущено** в столбце **Состояние** для виртуальной машины DC1.  
2.	На панели задач щелкните **Подключиться**. 
3.	При появлении запроса на открытие DC1.rdp щелкните **Открыть**.
4.	При появлении запроса в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Подключиться**.
5.	При появлении запроса используйте следующие учетные данные:
- Имя: **DC1\**[имя учетной записи локального администратора].
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

1.	На портале управления Azure на странице виртуальной машины щелкните **Работает** в столбце **Состояние** для виртуальной машины DC1.
2.	На панели задач щелкните **Подключиться**. 
3.	При появлении запроса на открытие DC1.rdp щелкните **Открыть**.
4.	При появлении запроса в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Подключиться**.
5.	При появлении запроса используйте следующие учетные данные:
- Имя: **CORP\**[имя учетной записи локального администратора].
- Пароль: [пароль учетной записи локального администратора]
6.	После появления запроса, ссылающегося на сертификаты, в окне сообщений «Подключение к удаленному рабочему столу» щелкните **Да**.

Теперь в Active Directory создайте учетную запись пользователя, которая будет использоваться при входе на компьютер-член домена CORP. Выполните по одной следующие команды в командной строке Windows PowerShell с правами администратора.
 
	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false 
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Обратите внимание, что после выполнения первой команды вы получите запрос на ввод пароля учетной записи User1. Выберите надежный пароль, так как эта учетная запись будет использоваться для подключения к удаленному рабочему столу на всех компьютерах-членах домена CORP. Чтобы проверить его надежность, см. раздел [Проверка надежности пароля. Использование надежных паролей](https://www.microsoft.com/security/pc-security/password-checker.aspx). Запишите пароль учетной записи User1 и сохраните его в надежном месте.

Подключитесь к виртуальной машине DC1 повторно, используя учетную запись CORP\User1.

Теперь, чтобы обеспечить трафик для программы Ping, выполните следующую команду в командной строке Windows PowerShell с правами администратора.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Это текущая конфигурация.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG02.png)

## Этап 3. Настройка APP1

APP1 предоставляет веб-службы общего доступа и службы обмена файлами.

Чтобы создать виртуальную машину Azure для APP1, сначала введите имя своей облачной службы и выполните следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for APP1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name APP1 -InstanceSize Small -ImageName $image 
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Затем подключитесь к виртуальной машине APP1, используя учетные данные CORP\User1, и откройте командную строку Windows PowerShell с правами администратора.

Чтобы проверить разрешение имен и сетевое подключение между APP1 и DC1, выполните команду **ping dc1.corp.contoso.com** и убедитесь, что возвращено четыре ответа.

Далее настройте APP1 в качестве веб-сервера, выполнив следующую команду в командной строке Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Теперь создайте общую папку и текстовый файл в этой папке в APP1, используя следующие команды.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Это текущая конфигурация.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG03.png)

## Этап 4. Настройка CLIENT1

CLIENT1 работает в интрасети Contoso как обычный ноутбук, планшет или настольный компьютер.

Чтобы создать виртуальную машину Azure для CLIENT1, сначала введите имя своей облачной службы и выполните следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for CLIENT1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name CLIENT1 -InstanceSize Small -ImageName $image 
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Затем подключитесь к виртуальной машине CLIENT1, используя учетные данные CORP\User1.

Чтобы проверить разрешение имен и сетевое подключение между CLIENT1 и DC1, выполните команду **ping dc1.corp.contoso.com** в командной строке Windows PowerShell и убедитесь, что возвращено четыре ответа.

Теперь убедитесь, что из CLIENT1 можно получить доступ к общим веб- и файловым ресурсам в APP1.

1.	В диспетчере серверов в древовидном представлении щелкните **Локальный сервер**. 
2.	В **Свойства для CLIENT1** щелкните **Включено** возле **Конфигурация усиленной безопасности Internet Explorer**.
3.	В **Конфигурация усиленной безопасности Internet Explorer** щелкните **Выключено** для **Администраторы** и **Пользователи** и нажмите кнопку **OK**.
4.	На начальном экране щелкните **Internet Explorer**, а затем нажмите кнопку **OK**.
5.	В адресной строке введите **http://app1.corp.contoso.com/** и нажмите клавишу ВВОД. Вы должны увидеть веб-страницу по умолчанию «Службы IIS» для APP1.6.	На панели задач рабочего стола щелкните значок «Проводник».
7.	В адресной строке введите **\\app1\Files** и нажмите клавишу ВВОД.
8.	Вы должны увидеть окно папки с содержимым, которое находится в общей папке «Файлы».
9.	В окне общей папки **Файлы** дважды щелкните файл **Example.txt**. Вы увидите содержимое этого файла.
10.	Закройте **example.txt — Блокнот** и окна общей папки **Файлы**.

Это последнее изменение конфигурации.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Теперь базовая конфигурация в Azure готова к разработке и тестированию приложений и дополнительных тестовых сред, таких как [смоделированная гибридная облачная среда](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).

## Дополнительные ресурсы

[Гибридные облачные тестовые среды](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)

 
## <a id="costs"></a>Снижение затрат на виртуальные машины тестовой среды в Azure.

Чтобы уменьшить затраты на работу виртуальных машин в тестовой среде, можно использовать один из следующих способов.

- Создайте тестовую среду и выполните необходимые тестирования и демонстрации как можно быстрее. По завершении удалите виртуальные машины тестовой среды.
- Завершите работу виртуальных машин в тестовой среде так, чтобы они перешли в освобожденное состояние. 

Чтобы завершить работу виртуальных машин с помощью Azure PowerShell, введите имя облачной службы и выполните следующие команды.

	$serviceName="<your cloud service name>"
	Stop-AzureVM -ServiceName $serviceName -Name "CLIENT1"
	Stop-AzureVM -ServiceName $serviceName -Name "APP1"
	Stop-AzureVM -ServiceName $serviceName -Name "DC1" -Force -StayProvisioned


Чтобы при запуске виртуальные машины, которые пребывают в состоянии «Остановлена» («Освобождена»), работали надлежащим образом, эти машины следует запустить в следующем порядке:

1.	DC1
2.	APP1
3.	CLIENT1 

Чтобы запустить виртуальные машины по порядку с помощью Azure PowerShell, введите имя облачной службы и выполните следующие команды.

	$serviceName="<your cloud service name>"
	Start-AzureVM -ServiceName $serviceName -Name "DC1"
	Start-AzureVM -ServiceName $serviceName -Name "APP1"
	Start-AzureVM -ServiceName $serviceName -Name "CLIENT1"
 

<!---HONumber=July15_HO4-->