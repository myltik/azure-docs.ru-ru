<properties 
	pageTitle="Настройка фермы SharePoint интрасети в гибридном облаке для тестирования" 
	description="Узнайте, как создать ферму SharePoint интрасети в гибридной облачной среде для разработки или тестирования ИТ-специалистами." 
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
	ms.date="03/05/2015" 
	ms.author="josephd"/>


# Настройка фермы SharePoint интрасети в гибридном облаке для тестирования

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

Существует три основных этапа настройки данной тестовой гибридной облачной среды:

1.	настройка гибридной облачной среды для тестирования.
2.	настройка компьютера с SQL Server (SQL1).
3.	настройка сервера SharePoint (SP1).

Если у вас еще нет подписки Azure, можно зарегистрироваться для получения бесплатной пробной версии на веб-сайте [Try Azure](http://azure.microsoft.com/pricing/free-trial/). При наличии подписки MSDN см. раздел [Преимущества Azure для подписчиков MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Этап 1. Настройка гибридной облачной среды

Используйте инструкции в разделе [Настройка гибридной облачной среды для тестирования](virtual-networks-setup-hybrid-cloud-environment-testing.md). Поскольку для данной тестовой среды не нужен сервер APP1 в подсети Corpnet, можно его спокойно выключить на время.

Это текущая конфигурация.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_1.png)
 
## Этап 2. Настройка компьютера с SQL Server (SQL1).

Из портала управления Azure запустите компьютер DC2, если это необходимо.

Сначала создайте подключение удаленного рабочего стола к DC2, используя учетные данные CORP\User1.

Далее создайте учетную запись администратора фермы SharePoint. Откройте командную строку Windows PowerShell с правами администратора в DC2 и выполните следующую команду.

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

При появлении запроса укажите пароль учетной записи SPFarmAdmin. Введите надежный пароль и запишите его в безопасном месте.

Теперь создайте виртуальную машину Azure для SQL1, выполнив следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<The password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Затем подключитесь к новой виртуальной машине SQL1 *using the local administrator account*.

1.	На левой панели портала управления Azure щелкните **Виртуальные машины**, а затем щелкните **Работающие** в столбце Состояние для SQL1.
2.	В панели задач щелкните **Подключить**. 
3.	При появлении запроса на открытие SQL1.rdp щелкните **Открыть**.
4.	При появлении запроса в окне сообщений "Подключение к удаленному рабочему столу" щелкните **Подключить**.
5.	При появлении запроса используйте следующие учетные данные:
	- Имя: **SQL1**[имя учетной записи локального администратора]
	- Пароль: [пароль учетной записи локального администратора]
6.	После появления запроса, ссылающегося на сертификаты, в окне сообщений "Подключение к удаленному рабочему столу" щелкните **Да**.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения и SQL Server. Из командной строки Windows PowerShell с правами администратора на SQL1 выполните следующие команды.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

В результате выполнения команды ping должны быть получены четыре успешных ответа с IP-адреса 10.0.0.1.

Далее следует добавить дополнительный диск данных как новый том с буквой диска F:.

1.	В левой панели диспетчера серверов щелкните **Файловые службы и службы хранилища**, а затем щелкните **Диски**.
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
	- Для элемента **Данные** введите путь **f:\Data**.
	- Для элемента **Журнал** введите путь **f:\Log**.
	- Для элемента **Резервная копия** введите путь **f:\Backup**.
	- Обратите внимание, что только новые базы данных используют эти расположения.
6.	Нажмите кнопку **OK**, чтобы закрыть окно.
7.	В древовидном представлении **обозревателя объектов** откройте **Безопасность**.
8.	Щелкните правой кнопкой мыши **Имена для входа**, а затем щелкните **Новое имя для входа**.
9.	В поле **Имя для входа** введите **CORP\User1**.
10.	На странице **Роли сервера** щелкните **sysadmin**, а затем нажмите кнопку **OK**.
11.	В древовидном представлении **обозревателя объектов** щелкните правой кнопкой мыши **Имена для входа**, а затем щелкните **Новое имя для входа**.
12.	На странице **Общие** в поле **Имя для входа** введите **CORP\SPFarmAdmin**.
13.	На странице **РОли сервера** выберите **dbcreator**, а затем нажмите кнопку **OK**.
14.	Закройте Microsoft SQL Server Management Studio.

Это текущая конфигурация.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_2.png)

 
## Этап 3. Настройка сервера SharePoint (SP1)

Сначала создайте виртуальную машину Azure для SP1, выполнив следующие команды в командной строке Azure PowerShell на локальном компьютере.

	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<The password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$vm1=New-AzureVMConfig -Name SP1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Затем подключитесь к виртуальной машине SP1 с учетными данными CORP\User1.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения. Из командной строки Windows PowerShell с правами администратора на SP1 выполните следующие команды.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

В результате выполнения команды ping должны быть получены четыре успешных ответа с IP-адреса 10.0.0.1.

После этого настройте SP1 для новой фермы SharePoint и сайта рабочей группы по умолчанию.

1.	На начальном экране введите **Продукты SharePoint 2013**, а затем щелкните **Мастер настройки продуктов SharePoint 2013**. При запросе на разрешение программе вносить изменения на компьютере щелкните **Да**.
2.	На странице Продукты SharePoint щелкните **Далее**. 
3.	В диалоговом окне, уведомляющем о том, что некоторые службы может понадобиться перезапустить во время настройки, щелкните **Да**.
4.	На странице "Подключение к ферме серверов" щелкните **Создать новую ферму серверов**, а затем щелкните **Далее**.
5.	На странице "Указание параметров базы данных конфигурации" введите **sql1.corp.contoso.com** в поле **Сервер базы данных**, введите **CORP\SPFarmAdmin** в поле **Имя пользователя**, введите пароль учетной записи SPFarmAdmin в поле **Пароль**, а затем щелкните **Далее**.
6.	На странице "Задание параметров безопасности фермы" введите **P@ssphrase** в поля **Парольная фраза** и **Подтверждение парольной фразы**, а затем щелкните **Далее**.
7.	На странице "Настройка веб-приложения центра администрирования SharePoint" щелкните **Далее**.
8.	На странице "Завершение мастера настройки продуктов SharePoint" щелкните **Далее**. Завершение мастера настройки продуктов SharePoint может занять несколько минут.
9.	На странице "Настройка выполнена успешно" нажмите кнопку **Готово**. После завершения Internet Explorer запускается с вкладки с именем "Мастер начальной настройки фермы".
10.	В диалоговом окне **Помогите сделать SharePoint лучше** щелкните **Нет, я не хочу участвовать** и нажмите кнопку **OK**.
11.	Для окна **Как следует выполнить настройку фермы SharePoint?** щелкните **Запустить мастер**.
12.	На странице "Настройка фермы SharePoint" в разделе **Учетная запись службы** щелкните **Использовать существующую управляемую учетную запись**.
13.	В окне **Службы** снимите все флажки за исключением флажка рядом с полем **Служба состояний**, а затем щелкните **Далее**. На короткое время может отображаться страница "Выполняется" до завершения.
14.	На странице "Создание семейства веб-сайтов" в разделе **Название и описание** введите **Contoso Corporation** в поле **Название**, укажите URL-адрес **http://sp1**/, а затем нажмите кнопку **OK**. На короткое время может отображаться страница "Выполняется" до завершения. На этом шаге создается сайт рабочей группы с URL-адресом http://sp1.
15.	На странице "Завершение работы мастера настройки фермы" нажмите кнопку **Готово**. На вкладке Internet Explorer отображается сайт центра администрирования SharePoint 2013.
16.	Войдите на компьютер CLIENT1 с учетными данными CORP\User1, а затем запустите Internet Explorer.
17.	В адресной строке введите **http://sp1/** и нажмите клавишу ВВОД. Должен появиться сайт рабочей группы в SharePoint для Contoso Corporation. Сайту может потребоваться некоторое время для подготовки к просмотру.

Это текущая конфигурация.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
Ферма SharePoint интрасети в гибридной облачной среде теперь готова для тестирования.

## Дополнительные ресурсы

[SharePoint в службах инфраструктуры Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Ферма серверов SharePoint](../virtual-machines/virtual-machines-sharepoint-farm-azure-preview.md)

[Настройка гибридной облачной среды для тестирования](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Настройка веб бизнес-приложения в гибридном облаке для тестирования](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Настройка синхронизации каталогов (DirSync) Office 365 в гибридном облаке для тестирования](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[Настройка смоделированной гибридной облачной среды для тестирования](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)
<!--HONumber=47-->
 