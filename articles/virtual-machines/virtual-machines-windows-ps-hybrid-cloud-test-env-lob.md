---
title: Тестовая среда бизнес-приложений | Microsoft Docs
description: Научитесь создавать веб бизнес-приложения в гибридной облачной среде для ИТ-специалистов или тестирования разработки.
services: virtual-machines-windows
documentationcenter: ''
author: JoeDavies-MSFT
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: josephd

---
# Настройка веб бизнес-приложения в гибридном облаке для тестирования
В этом разделе описываются шаги по созданию имитации гибридной облачной среды для тестирования сетевого бизнес-приложения, размещенного в Microsoft Azure. Это конфигурация, которая получается в результате.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

В эту конфигурацию входят:

* имитация локальной сети, размещенная в Azure (виртуальная сеть TestLab);
* виртуальная сеть с подключением между организациями, размещенная в Azure (TestVNET);
* VPN-подключение типа "виртуальная сеть — виртуальная сеть";
* сервер сетевого бизнес-приложения, SQL Server и дополнительный контроллер домена в виртуальной сети TestVNET.

Это основа и общая начальная точка, на базе которой можно:

* разрабатывать и тестировать бизнес-приложения, размещенные в службах IIS, с помощью сервера базы данных SQL Server 2014 в Azure.
* выполнять тестирование имитации гибридной облачной рабочей нагрузки ИТ-среды.

Существует три основных этапа настройки данной тестовой среды гибридного облака:

1. Создание имитации гибридной облачной среды.
2. настройка компьютера с SQL Server (SQL1).
3. настройка сервера с бизнес-приложением (LOB1).

Для этой рабочей нагрузки требуется подписка Azure. Если у вас есть подписка MSDN или Visual Studio, ознакомьтесь с разделом [Ежемесячная сумма денег на счете в Azure для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Пример рабочего бизнес-приложения, размещенного в Azure, можно найти в схеме архитектурного проекта **Бизнес-приложения** в разделе [Схемы и проекты архитектуры программного обеспечения Майкрософт](http://msdn.microsoft.com/dn630664).

## Этап 1. Создание имитации гибридной облачной среды
Создайте [имитацию тестовой гибридной облачной среды](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). Для этой тестовой среды не нужен сервер APP1 в корпоративной подсети, поэтому его можно выключить на время.

Это текущая конфигурация.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)

## Этап 2. Настройка компьютера с SQL Server (SQL1)
На портале Azure запустите компьютер DC2, если это необходимо.

Теперь создайте виртуальную машину для SQL1, выполнив следующие команды в командной строке Azure PowerShell на локальном компьютере. Перед выполнением этих команд заполните значения переменных и удалите знаки < и >.

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

Используйте портал Azure, чтобы подключиться к SQL1 с использованием учетной записи локального администратора SQL1.

Далее настройте правила брандмауэра Windows, чтобы разрешить простое тестирование подключения и трафик SQL Server. Из командной строки Windows PowerShell с правами администратора на SQL1 выполните следующие команды.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

В результате выполнения команды ping должны возвратиться четыре успешных ответа с IP-адреса 192.168.0.4.

Далее на SQL1 следует добавить дополнительный диск данных как новый том с буквой диска F:.

1. В левой области диспетчера серверов щелкните **Файловые службы и службы хранилища**, а затем щелкните **Диски**.
2. В области содержимого в группе **Диски** щелкните **диск 2** (при этом для параметра **Раздел** должно быть задано значение **Неизвестный**).
3. Щелкните **Задачи**, а затем **Новый том**.
4. На странице «Перед началом работы» мастера создания томов щелкните **Далее**.
5. На странице «Выбор сервера и диска» щелкните **Диск 2**, а затем щелкните **Далее**. При появлении запроса нажмите кнопку **OK**.
6. На странице «Выбор размера тома» щелкните **Далее**.
7. На странице «Назначение букве диска или папке» щелкните **Далее**.
8. На странице «Выбор параметров файловой системы» щелкните **Далее**.
9. На странице «Подтверждение выбора» щелкните **Создать**.
10. После завершения нажмите кнопку **Закрыть**.

Выполните следующие команды из командной строки Windows PowerShell на SQL1:

    md f:\Data
    md f:\Log
    md f:\Backup

Затем присоедините SQL1 к домену Windows Server Active Directory CORP, выполнив следующие команды в командной строке Windows PowerShell на SQL1.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

При появлении запроса на ввод учетных данных домена для команды **Add-Computer** используйте учетную запись CORP\\User1.

После перезапуска используйте портал Azure, чтобы подключиться к SQL1 с помощью *учетной записи локального администратора SQL1*.

Далее настройте SQL Server 2014 для использования диска F: для новых баз данных и для разрешений учетной записи пользователя.

1. На начальном экране введите **SQL Server Management**, а затем щелкните **SQL Server 2014 Management Studio**.
2. В окне **Подключение к серверу** щелкните **Подключить**.
3. В древовидном представлении обозревателя объектов щелкните правой кнопкой мыши **SQL1**, а затем щелкните **Свойства**.
4. В окне **Свойства сервера** щелкните **Параметры базы данных**.
5. Найдите **Места хранения, используемые базой данных по умолчанию** и задайте следующие значения:
   * Для элемента **Данные** введите путь **f:\\Data**.
   * Для элемента **Журнал** введите путь **f:\\Log**.
   * Для элемента **Резервное копирование** введите путь **f:\\Backup**.
   * Примечание. Только новые базы данных будут использовать эти расположения.
6. Нажмите кнопку **ОК**, чтобы закрыть окно.
7. В древовидном представлении **обозревателя объектов** откройте **Безопасность**.
8. Щелкните правой кнопкой мыши **Имена входа**, а затем выберите **Создать имя входа**.
9. В поле **Имя входа** введите **CORP\\User1**.
10. На странице **Роли сервера** щелкните **sysadmin**, а затем нажмите кнопку **ОК**.
11. Закройте Microsoft SQL Server Management Studio.

Это текущая конфигурация.

.![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)

## Этап 3. Настройка сервера с бизнес-приложением (LOB1)
Сначала создайте виртуальную машину для LOB1, выполнив следующие команды в командной строке Azure PowerShell на локальном компьютере.

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

Затем используйте портал Azure, чтобы подключиться к LOB1 с помощью учетных данных локального администратора LOB1.

Далее настройте правила брандмауэра Windows, чтобы разрешить трафик для тестирования базовых параметров подключения. Из командной строки Windows PowerShell с правами администратора на LOB1 выполните следующие команды.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

В результате выполнения команды ping должны возвратиться четыре успешных ответа с IP-адреса 192.168.0.4.

Затем присоедините виртуальную машину LOB1 к домену Active Directory CORP, выполнив следующие команды в командной строке Windows PowerShell.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

При появлении запроса на ввод учетных данных домена для команды **Add-Computer** используйте учетную запись CORP\\User1.

После перезапуска используйте портал Azure для подключения к LOB1 с помощью учетной записи CORP\\User1 и ее пароля.

Затем настройте LOB1 для служб IIS и протестируйте доступ из CLIENT1.

1. В диспетчере серверов выберите **Добавить роли и компоненты**.
2. На странице **Перед началом работы** щелкните **Далее**.
3. На странице **Выбор типа установки** нажмите кнопку **Далее**.
4. На странице **Выбор целевого сервера** щелкните **Далее**.
5. На странице **Роли сервера** в списке **Роли** щелкните **Веб-сервер (IIS)**.
6. При появлении запроса нажмите кнопку **Добавить компоненты**, а затем щелкните **Далее**.
7. На странице **Выбор компонентов** щелкните **Далее**.
8. На странице **Веб-сервер (IIS)** щелкните **Далее**.
9. На странице **Выбор служб ролей** установите или снимите флажки для служб, которые необходимы для тестирования бизнес-приложения, а затем щелкните **Далее**.
10. На странице **Подтверждение выбранных элементов для установки** щелкните **Установить**.
11. Дождитесь завершения установки компонентов и нажмите кнопку **Закрыть**.
12. На портале Azure подключитесь к компьютеру CLIENT1 с учетными данными CORP\\User1, а затем запустите Internet Explorer.
13. В адресной строке введите **http://lob1/** и нажмите клавишу ВВОД. Вы увидите веб-страницу по умолчанию IIS 8.

Это текущая конфигурация.

.![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Эта среда теперь готова для развертывания веб-приложения на LOB1, а также тестирования функциональности с компьютера CLIENT1 в корпоративной подсети.

## Дальнейшие действия
* Добавьте новую виртуальную машину с помощью [портала Azure](virtual-machines-windows-hero-tutorial.md).

<!---HONumber=AcomDC_0810_2016-->