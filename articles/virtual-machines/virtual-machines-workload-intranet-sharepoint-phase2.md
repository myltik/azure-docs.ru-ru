<properties
	pageTitle="Ферма SharePoint Server 2013, этап 2 | Microsoft Azure"
	description="На втором этапе развертывания SharePoint Server 2013 в Azure создайте и настройте два контроллера доменов реплик Active Directory."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Ферма SharePoint в интрасети, этап 2: настройка контроллеров домена

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


На этом этапе развертывания фермы SharePoint 2013 в инстрасети с группами доступности AlwaysOn для SQL Server на базе служб инфраструктуры Azure создаются два контроллера домена в виртуальной сети Azure в управлении службами. Интернет-запросы клиента к ресурсам фермы SharePoint могут проходить аутентификацию в виртуальной сети Azure вместо отправки трафика аутентификации через VPN или подключение Azure ExpressRoute в локальную сеть.

Этот этап необходимо выполнить, прежде чем переходить к [этапу 3](virtual-machines-workload-intranet-sharepoint-phase3.md). Описания всех этапов см. в разделе [Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

## Создание виртуальных машин контроллеров домена в среде Azure

Сначала необходимо заполнить столбец **Имя виртуальной машины** таблицы M и скорректировать размеры виртуальных машин в столбце **Минимальный размер**.

Элемент | Имя виртуальной машины | Образ из коллекции | Минимальный размер
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (первый контроллер домена, например DC1) | Центр обработки данных Windows Server 2012 R2 | A2 (средний)
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (второй контроллер домена, например DC2) | Центр обработки данных Windows Server 2012 R2 | A2 (средний)
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (первый компьютер SQL Server, например SQL1) | Microsoft SQL Server 2014 Enterprise — Windows Server 2012 R2 | 	A7
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (второй компьютер SQL Server, например SQL2) | Microsoft SQL Server 2014 Enterprise — Windows Server 2012 R2 | 	A7
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (ресурс-свидетель большинства узлов для кластера, например MN1) | Центр обработки данных Windows Server 2012 R2 | A1 (малый)
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (первый сервер приложений SharePoint, например APP1) | Microsoft SharePoint Server 2013 Trial — Windows Server 2012 R2 | A4 (очень большой)
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (второй сервер приложений SharePoint, например APP2) | Microsoft SharePoint Server 2013 Trial — Windows Server 2012 R2 | A4 (очень большой)
8\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (первый веб-сервер SharePoint, например WEB1) | Microsoft SharePoint Server 2013 Trial — Windows Server 2012 R2 | A4 (очень большой)
9\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (второй веб-сервер SharePoint, например WEB2) | Microsoft SharePoint Server 2013 Trial — Windows Server 2012 R2 | A4 (очень большой)

**Таблица M: виртуальные машины для фермы SharePoint 2013 для интрасети на базе Azure**

Полный список размеров виртуальных машин см. статье [Размеры виртуальных машин](virtual-machines-size-specs.md).

С помощью приведенного ниже набора команд Azure PowerShell создайте виртуальные машины для двух контроллеров домена. Укажите значения переменных, удалив знаки < and >. Обратите внимание, что в наборе команд Azure PowerShell используются значения из перечисленных ниже таблиц:

- Таблица M (для виртуальных машин)
- Таблица V (для параметров виртуальной сети)
- Таблица S (для подсети)
- Таблица A (для групп доступности)
- Таблица C (для облачных служб)

Значения в таблицах V, S, A и C были заданы на [этапе 1 «Настройка Azure»](virtual-machines-workload-intranet-sharepoint-phase1.md).

Указав все необходимые значения, выполните полученные команды в среде Azure PowerShell.

	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 1 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table S – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 6 – Value column>

	$serviceName="<Table C – Item 1 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 7 – Value column>

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## Настройка первого контроллера домена

Войдите на компьютер первого контроллера домена с помощью учетной записи локального администратора.

### <a id="logon"></a>Вход на виртуальную машину с помощью подключения удаленного рабочего стола

1.	В левой панели на портале Azure щелкните **Виртуальные машины**.
2.	Чтобы подключиться к виртуальной машине, щелкните **Работает** в столбце **Состояние** рядом с ее именем.
3.	На панели команд в нижней части страницы щелкните **Подключиться**.
4.	Портал сообщит, что извлекается RDP-файл. Нажмите кнопку **ОК**.
5.	Появится диалоговое окно браузера с предложением открыть или сохранить файл имя\_компьютера.rdp с веб-сайта manage.windowsazure.com. Щелкните **Открыть**.
6.	В диалоговом окне **Подключение к удаленному рабочему столу** нажмите кнопку **Подключиться**.
7.	В диалоговом окне **Безопасность Windows** выберите **Другая учетная запись**.
8.	В поле **Имя пользователя** введите имя виртуальной машины и имя пользователя учетной записи локального администратора на этой машине (локальная запись на этом компьютере). Используйте следующий формат: *имя\_компьютера*\*учетная\_запись\_локального\_администратора*.
9.	В поле **Пароль** введите пароль к учетной записи локального администратора.
10.	Нажмите кнопку **ОК**.
11.	В диалоговом окне **Подключение к удаленному рабочему столу** нажмите кнопку **Да**. В окне сеанса удаленного подключения появится рабочий стол нового компьютера.

Теперь добавьте на первый контроллер домена дополнительный диск данных.

### <a id="datadisk"></a>Инициализация пустого диска

1.	В левой области диспетчера серверов щелкните **Файловые службы и службы хранилища**, а затем выберите **Диски**.
2.	В области содержимого в группе **Диски** щелкните **диск 2** (при этом для параметра **Раздел** должно быть задано значение **Неизвестный**).
3.	Щелкните **Задачи**, а затем **Новый том**.
4.	На странице **Перед началом работы** мастера создания томов нажмите кнопку **Далее**.
5.	На странице **Выбор сервера или диска** щелкните **Диск 2**, а затем нажмите кнопку **Далее**. При появлении запроса нажмите кнопку **OK**.
6.	На странице **Выбор размера тома** нажмите кнопку **Далее**.
7.	На странице **Назначение букве диска или папке** нажмите кнопку **Далее**.
8.	На странице **Выбор параметров файловой системы** нажмите кнопку **Далее**.
9.	На странице **Подтверждение выбора** щелкните **Создать**.
10.	После завершения инициализации щелкните **Закрыть**.

Теперь проверьте, доступны ли с контроллера домена ресурсы в сети вашей организации.

### <a id="testconn"></a>Проверка подключения

1.	На рабочем столе откройте консоль Windows PowerShell.
2.	Используйте команду **ping**, чтобы проверить связь с именами и IP-адресами ресурсов в сети организации.

Эта процедура позволяет убедиться в том, что DNS-имена разрешаются правильно (виртуальная машина правильно настроена для работы с локальными DNS-серверами), а пакеты данных можно отправлять в распределенную виртуальную сеть и из нее.

Затем в командной строке Windows PowerShell на первом контроллере домена выполните следующие команды:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Компьютер перезапустится.

## Настройка второго контроллера домена

Войдите на компьютер второго контроллера домена с помощью учетной записи его локального администратора. Соответствующие инструкции см. в разделе [Вход на виртуальную машину с использованием подключения к удаленному рабочему столу](#logon).

Теперь добавьте на второй контроллер домена дополнительный диск данных. См. раздел [Инициализация пустого диска](#datadisk).

Теперь проверьте, доступны ли со второго контроллера домена ресурсы в сети вашей организации. См. раздел [Проверка подключения](#testconn). Эта процедура позволяет убедиться в том, что DNS-имена разрешаются правильно (виртуальная машина правильно настроена для работы с локальными DNS-серверами), а пакеты данных можно отправлять в распределенную виртуальную сеть и из нее.

Затем в командной строке Windows PowerShell на втором контроллере домена выполните следующие команды:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Компьютер перезапустится.

## Настройка учетных записей и разрешений фермы SharePoint

На ферме SharePoint понадобятся перечисленные ниже учетные записи пользователей.

- sp\_farm: учетная запись для управления фермами SharePoint.
- sp\_farm\_db: учетная запись с правами sysadmin на экземплярах SQL Server.
- sp\_install: учетная запись с правами администратора домена, необходимыми для установки ролей и компонентов.
- sqlservice: учетная запись, с помощью которой можно запускать экземпляры SQL Server.

Затем войдите на любой из контроллеров домена с учетной записью администратора соответствующего домена, откройте консоль Windows PowerShell с правами администратора и выполните следующие команды *по одной*:

	New-ADUser -SamAccountName sp_farm -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_farm_db -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm_db" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_install -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_install" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-	ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

Для каждой команды вам будет предложено ввести пароль. Запишите эти имена пользователей и пароли и сохраните их в надежном месте.

Затем добавьте в учетные записи новых пользователей дополнительные свойства с помощью описанных ниже действий.

1.	На начальном экране введите **Пользователи Active Directory**, и щелкните **Пользователи и компьютеры Active Directory**.
2.	В древовидном представлении откройте узел своего домена и щелкните **Пользователи**.
3.	В области содержимого щелкните правой кнопкой мыши элемент **sp\_install** и выберите команду **Добавить в группу**.
4.	В диалоговом окне **Выбор групп** введите **domain admins** и дважды нажмите кнопку **ОК**.
5.	В диалоговом окне щелкните «Вид» и выберите **Дополнительные параметры**. В результате вы сможете увидеть все скрытые контейнеры и вкладки окна свойств для объектов Active Directory.
6.	Щелкните имя своего домена правой кнопкой мыши и выберите **Свойства**.
7.	В диалоговом окне **Свойства** откройте вкладку **Безопасность** и нажмите кнопку **Дополнительно**.
8.	В окне **Дополнительные параметры безопасности для домена <YourDomain>** щелкните **Добавить**.
9.	В окне **Элемент разрешения для <YourDomain>** щелкните **Выберите субъект**.
10.	В текстовое поле введите **<YourDomain>\\sp\_install** и нажмите кнопку **ОК**.
11.	Выберите **Разрешить** для права на **создание объектов-компьютеров** и трижды нажмите кнопку **ОК**.

Теперь измените параметры DNS-серверов в своей виртуальной сети: виртуальные машины в среде Azure должны использовать в качестве DNS-серверов IP-адреса двух новых контроллеров домена. Обратите внимание на то, что для этой процедуры используются значения из таблицы V с параметрами виртуальной сети.

1.	На левой панели портала Azure щелкните **Сети** и выберите имя своей виртуальной сети (таблица V, элемент 1, столбец "Значение").
2.	Нажмите **Настроить**.
3.	В разделе **DNS-серверы** удалите записи для DNS-серверов, расположенных в вашей локальной сети.
4.	В разделе **DNS-серверы** добавьте две записи с понятными именами и IP-адресами из следующих строк таблицы:
 - Таблица V, элемент 6, столбец «Значение»
 - Таблица V, элемент 7, столбец «Значение»
5.	На панели команд в нижней части экрана щелкните **Сохранить**.
6.	В левой области портала Azure выберите **Виртуальные машины**, а затем щелкните столбец **Состояние** рядом с именем первого контроллера домена.
7.	На панели команд нажмите кнопку **Перезапустить**.
8.	После запуска первого контроллера домена щелкните столбец **Состояние** рядом с именем второго контроллера домена.
9.	На панели команд нажмите кнопку **Перезапустить**. Дождитесь запуска второго контроллера домена.

Перезапуск контроллеров необходим для того, чтобы они перестали использовать локальные DNS-серверы. Так как оба контроллера сами являются DNS-серверами, при повышении их уровня до контроллеров домена локальные DNS-серверы были автоматически назначены им в качестве DNS-серверов пересылки.

Теперь необходимо создать сайт репликации Active Directory, чтобы все серверы в виртуальной сети Azure использовали локальные контроллеры домена. Войдите на основной контроллер домена с учетной записью sp\_install и выполните следующие команды из консоли Windows PowerShell с правами администратора:

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

На этой схеме показана конфигурация после успешного выполнения этого этапа (используются заполнители имен компьютеров):

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## Дальнейшие действия

Дальнейшие действия по настройке этой рабочей нагрузки описаны в статье [Этап 3: настройка инфраструктуры SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md).

## Дополнительные ресурсы

[Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Фермы SharePoint, размещенные в службах инфраструктуры Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Инфографика SharePoint с SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Руководство по реализации служб инфраструктуры Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Службы инфраструктуры Azure: высокодоступное бизнес-приложение](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Oct15_HO3-->