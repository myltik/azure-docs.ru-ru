<properties 
	pageTitle="Настройка веб бизнес-приложения в гибридном облаке для тестирования" 
	description="Научитесь создавать веб бизнес-приложения в гибридной облачной среде для ИТ-специалистов или тестирования разработки." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="josephd"/>

#Настройка веб бизнес-приложения в гибридном облаке для тестирования

В этом разделе описываются шаги по созданию гибридной облачной среды для тестирования бизнес-приложения интрасети, размещенного в Microsoft Azure. Это конфигурация, которая получается в результате.

![](./media/virtual-networks-set-up-LOB-App-hybrid-cloud-for-testing/CreateLOBAppHybridCloud_3.png)

Пример рабочего бизнес-приложения, размещенного в Azure, см. в подразделе **Архитектурные проекты бизнес-приложений** в разделе [Архитектурные схемы и проекты программного обеспечения Майкрософт](http://msdn.microsoft.com/dn630664).

Данная конфигурация имитирует бизнес-приложение в рабочей среде Azure из вашего расположения в Интернете. В ее состав входит:

- упрощенная  локальная сеть (подсеть Corpnet).
- виртуальная сеть с подключением между организациями, размещенная в Azure (TestVNET).
- VPN-подключение "сайт-сайт".
- сервер бизнес-приложения, SQL Server и дополнительный контроллер домена в виртуальной сети TestVNET.

Это основа и общая начальная точка, на базе которой можно:

- разрабатывать и тестировать бизнес-приложения, размещенные в службах IIS, с помощью сервера базы данных SQL Server 2014 в Azure.
- выполнять тестирование этой гибридной облачной рабочей нагрузки ИТ-среды.

Существует три основных этапа настройки данной тестовой гибридной облачной среды:

1.	настройка гибридной облачной среды для тестирования.
2.	настройка компьютера с SQL Server (SQL1).
3.	настройка сервера с бизнес-приложением (LOB1).

Если у вас еще нет подписки Azure, можно зарегистрироваться для получения бесплатной пробной версии на веб-сайте [Try Azure](http://www.windowsazure.com/pricing/free-trial/). При наличии подписки MSDN см. раздел [Преимущества Azure для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

##Этап 1. Настройка гибридной облачной среды

Используйте инструкции в разделе [Настройка гибридной облачной среды для тестирования](../virtual-networks-setup-hybrid-cloud-environment-testing/). Поскольку для данной тестовой среды не нужен сервер APP1 в подсети Corpnet, можно его спокойно выключить на время.

Это текущая конфигурация.

![](./media/virtual-networks-set-up-LOB-App-hybrid-cloud-for-testing/CreateLOBAppHybridCloud_1.png)
 
##Этап 2. Настройка компьютера с SQL Server (SQL1).

Из портала управления Azure запустите компьютер DC2, если это необходимо.

Теперь создайте виртуальную машину Azure для SQL1, выполнив следующие команды в командной строке Azure PowerShell с правами администратора на локальном компьютере. Перед выполнение этих команд заполните значения переменных и удалите символы < and >.

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<A password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Затем подключитесь к новой виртуальной машине SQL1 *using the local administrator account*.

1.	На левой панели портала управления Azure щелкните **Виртуальные машины**, а затем щелкните **Работающие** в столбце Состояние для SQL1.
2.	На панели задач щелкните **Подключить**. 
3.	При появлении запроса на открытие SQL1.rdp щелкните **Открыть**.
4.	При появлении запроса в окне сообщений "Подключение к удаленному рабочему столу" щелкните **Подключить**.
5.	При появлении запроса используйте следующие учетные данные:
	- Имя: **SQL1\\**[имя учетной записи локального администратора]
	- Пароль: [пароль учетной записи локального администратора]
6.	После появления запроса, ссылающегося на сертификаты, в окне сообщений "Подключение к удаленному рабочему столу" щелкните **Да**.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения и SQL Server. Из командной строки Windows PowerShell с правами администратора на SQL1 выполните следующие команды.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

В результате выполнения команды ping должны быть получены четыре успешных ответа с IP-адреса 10.0.0.1.

Далее следует добавить дополнительный диск данных как новый том с буквой диска F:.

1.	В левой панели диспетчера сервера щелкните  **Файловые службы и службы хранилища**, а затем щелкните **Диски**.
2.	На панели содержимого в группе **Диски** щелкните **диск 2** (с параметром **Раздел**, имеющим значение **Неизвестно**).
3.	Последовательно щелкните **Задачи**, а затем **Новый том**.
4.	На странице "Прежде чем начать" мастера создания томов щелкните **Далее**.
5.	На странице "Выбор сервера и диска" щелкните **Диск 2**, а затем щелкните **Далее**. При появлении запроса нажмите кнопку **OK**.
6.	На странице "Указание размера тома" щелкните **Далее**.
7.	На странице "Назначение букве диска или папке" щелкните **Далее**.
8.	На странице "Выбор параметров файловой системы" щелкните **Далее**.
9.	На странице "Подтверждение выбранных параметров" щелкните **Создать**.
10.	После завершения нажмите кнопку **Закрыть**.

Выполните следующие команды из командной строки Windows PowerShell на SQL1:

	md f:\Data
	md f:\Log
	md f:\Backup

Далее настройте SQL Server 2014 для использования диска "F:" для новых баз данных и для разрешений учетной записи пользователя.

1.	На начальном экране введите **Управление SQL Server**, а затем нажмите кнопку **SQL Server 2014 Management Studio**.
2.	В окне **Подключение к серверу** щелкните **Подключить**.
3.	В области дерева обозревателя объектов щелкните правой кнопкой мыши **SQL1**, а затем щелкните **Свойства**.
4.	В окне **Свойства сервера** щелкните **Параметры базы данных**.
5.	Найдите **Расположения баз данных по умолчанию** и задайте следующие значения: 
	- для элемента **Данные** введите путь **f:\Data**.
	- для элемента **Журнал** ведите путь **f:\Log**.
	- для элемента **Резервная копия** введите путь **f:\Backup**.
	- Примечание. Только новые базы данных будут использовать эти расположения.
6.	Нажмите кнопку **OK**, чтобы закрыть окно.
7.	В древовидном представлении **обозревателя объектов** откройте **Безопасность**.
8.	Щелкните правой кнопкой мыши **Имена для входа**, а затем щелкните **Новое имя для входа**.
9.	В поле **Имя для входа** введите **CORP\User1**.
10.	На странице **Роли сервера** щелкните **sysadmin**, а затем нажмите кнопку **OK**.
11.	Закройте Microsoft SQL Server Management Studio.

Это текущая конфигурация.

![](./media/virtual-networks-set-up-LOB-App-hybrid-cloud-for-testing/CreateLOBAppHybridCloud_2.png)
 
##Этап 3. Настройка сервера с бизнес-приложением (LOB1).

Сначала создайте виртуальную машину Azure для LOB1, выполнив следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<A password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name LOB1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Затем подключитесь к виртуальной машине LOB1 с учетными данными CORP\User1.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения. Из командной строки Windows PowerShell с правами администратора на LOB1 выполните следующие команды.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

В результате выполнения команды ping должны быть получены четыре успешных ответа с IP-адреса 10.0.0.1.

Затем настройте LOB1 для служб IIS и протестируйте доступ из CLIENT1.

1.	Запустите диспетчер сервера и нажмите кнопку **Добавить роли и компоненты**.
2.	На странице "Перед началом работы" нажмите кнопку **Далее**.
3.	На странице "Выбор типа установки" щелкните **Далее**.
4.	На странице "Выбор целевого сервера" щелкните  **Далее**.
5.	На странице "Роли сервера" щелкните **Веб-сервер (IIS)** в списке **Роли**.
6.	При появлении запроса нажмите кнопку **Добавить компоненты**, а затем щелкните **Далее**.
7.	На странице "Выбор компонентов" щелкните **Далее**.
8.	На странице "Веб-сервер (IIS)" щелкните **Далее**.
9.	На странице "Выбор служб ролей" установите или снимите флажки для служб, которые необходимы для тестирования бизнес-приложения, а затем щелкните **Далее**.
10.	На странице подтверждения выбранных параметров установки щелкните **Установить**.
11.	Дождитесь завершения установки компонентов и нажмите кнопку **Закрыть**.
12.	Войдите на компьютер CLIENT1 с учетными данными учетной записи CORP\User1, а затем запустите Internet Explorer.
13.	В адресной строке введите **http://lob1/** и нажмите клавишу ВВОД. Вы увидите веб-страницу по умолчанию IIS 8.

Это текущая конфигурация.

![](./media/virtual-networks-set-up-LOB-App-hybrid-cloud-for-testing/CreateLOBAppHybridCloud_3.png)
 
Данная среда теперь готова для развертывания веб-приложения на LOB1, а также тестирования функциональности и производительности из подсети Corpnet.

##Дополнительные ресурсы

[Архитектурные схемы и проекты программного обеспечения Майкрософт](http://msdn.microsoft.com/dn630664)

[Платформа поддержкой размещения веб-сервера (IIS)](http://technet.microsoft.com/library/hh831818)

[Настройка гибридной облачной среды для тестирования](../virtual-networks-setup-hybrid-cloud-environment-testing/)

[Настройка фермы SharePoint интрасети в гибридном облаке для тестирования](../virtual-networks-setup-sharepoint-hybrid-cloud-testing/)

[Настройка синхронизации каталогов (DirSync) Office 365 в гибридном облаке для тестирования](../virtual-networks-setup-dirsync-hybrid-cloud-testing/)
<!--HONumber=45--> 
