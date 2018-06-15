---
title: Настройка группы доступности Always On на виртуальной машине Azure с помощью PowerShell | Документация Майкрософт
description: В этом руководстве используются ресурсы, которые были созданы с помощью классической модели развертывания. Здесь вы будете создавать группы доступности Always On в Azure с помощью PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: fe7384baa6740d316fb5a8ec7b1894f337d88080
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401299"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Настройка группы доступности Always On на виртуальной машине Azure с помощью PowerShell
> [!div class="op_single_selector"]
> * [Классическая модель: пользовательский интерфейс](../classic/portal-sql-alwayson-availability-groups.md)
> * [Классическая модель: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Прежде чем начать, учтите, что теперь можно выполнить эту задачу в модели Azure Resource Manager. Для новых развертываний мы советуем использовать модель Azure Resource Manager. См. сведения в статье [Введение в группы доступности Always On SQL Server на виртуальных машинах Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Для большинства новых развертываний рекомендуется использовать модель Resource Manager. В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания.

Виртуальные машины Azure могут помочь администраторам баз данных сократить затраты при реализации более высокого уровня доступности системы SQL Server. В этом руководстве описано, как реализовать группу доступности с помощью сквозного соединения SQL Server Always On в среде Azure. Когда вы завершите, решение SQL Server Always On в Azure будет состоять из следующих элементов:

* Виртуальной сети, которая содержит множество подсетей, в том числе внешнюю и внутреннюю подсети;
* контроллера домена с доменом Active Directory;
* двух виртуальных машин SQL Server, развернутых во внутреннюю подсеть и присоединенных к домену Active Directory;
* отказоустойчивого кластера Windows из трех узлов с моделью кворума "Большинство узлов";
* группы доступности с двумя репликами с синхронной фиксацией базы данных доступности.

Этот сценарий — хороший выбор из-за его простоты на Azure, а не из-за его экономичности или других факторов. Например, можно сократить число виртуальных машин для группы доступности с двумя репликами, чтобы сократить часы вычислительных операций в Azure, используя контроллер домена как следящую общую папку кворума в отказоустойчивом кластере из двух узлов. Этот метод сократит число виртуальных машин на одну по сравнению с указанной выше конфигурацией.

В данном руководстве показаны шаги, необходимые для настройки описанного решения. Подробные сведения каждого этапа не приводятся. Вместо предоставления элементов графического пользовательского интерфейса на каждом этапе в руководстве используются скрипты PowerShell для быстрого ознакомления с этапами. В этом учебнике предполагается следующее:

* Вы уже имеете учетную запись Azure с подпиской для виртуальных машин.
* В системе установлены [командлеты Azure PowerShell](/powershell/azure/overview).
* Вы хорошо понимаете принцип работы групп доступности Always On в локальных решениях. Дополнительные сведения см. в разделе [Группы доступности AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Подключение к подписке Azure и создание виртуальной сети
1. В окне Powershell на локальном компьютере импортируйте модуль Azure, скачайте файл параметров публикации на компьютер и подключите сеанс PowerShell к подписке Azure путем импорта скачанных параметров публикации.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Команда **Get AzurePublishgSettingsFile** автоматически создает сертификат управления, а Azure скачивает его на компьютер. Автоматически откроется браузер, где потребуется ввести данные учетной записи Майкрософт для подписки Azure. Скачанный **PUBLISHSETTINGS**-файл содержит всю информацию, необходимую для управления подпиской Azure. После сохранения этого файла в локальный каталог импортируйте его с помощью команды **Import-AzurePublishSettingsFile**.

   > [!NOTE]
   > PUBLISHSETTINGS-файл содержит учетные данные (незакодированные), используемые для администрирования подписок и служб Azure. В целях безопасности этот файл рекомендуется временно хранить за пределами исходных каталогов (например, в папке Libraries\Documents), а затем удалить его после выполнения импорта. Злоумышленник, получивший доступ к PUBLISHSETTINGS-файлу, сможет изменять, создавать и удалять ваши службы Azure.

2. Определите ряд переменных, которые будут использоваться для создания облачной ИТ-инфраструктуры.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Чтобы обеспечить дальнейшее успешное выполнение команд, обратите внимание на следующее:

   * Переменные **$storageAccountName** и **$dcServiceName** должны быть уникальными, так как они используются для идентификации в Интернете соответственно облачной учетной записи хранения и облачного сервера.
   * Имена переменных **$affinityGroupName** и **$virtualNetworkName** задаются в документе конфигурации виртуальной сети, который будет использован позднее.
   * **$sqlImageName** указывает обновленное имя образа виртуальной машины, который содержит SQL Server 2012 Enterprise Edition с пакетом обновления 1 (SP1).
   * Для простоты во всем руководстве используется единственный пароль — **Contoso!000**.

3. Создайте территориальную группу

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Создайте виртуальную сеть, импортировав файл конфигурации.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Файл конфигурации содержит следующий XML-документ. Вкратце, он определяет виртуальную сеть под названием **ContosoNET** в территориальной группе, которая называется **ContosoAG**. Она имеет адресное пространство **10.10.0.0/16** и две подсети **10.10.1.0/24** и **10.10.2.0/24**, которые являются интерфейсной и конечной подсетями соответственно. В интерфейсной подсети можно разместить клиентские приложения, например Microsoft SharePoint, а в конечной подсети размещаются виртуальные машины SQL Server. Если переменные **$affinityGroupName** и **$virtualNetworkName** были изменены, следует изменить и соответствующие имена, приведенные ниже.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Создайте учетную запись хранения, связанную с созданной территориальной группой, а затем установите ее в качестве текущей учетной записи хранения в своей подписке.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Создайте сервер контроллера домена в новой облачной службе и группе доступности.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Эти переданные команды выполняют следующие задачи:

   * **New-AzureVMConfig** создает конфигурацию виртуальной машины.
   * **Add-AzureProvisioningConfig** задает параметры конфигурации отдельного сервера Windows.
   * **Add-AzureDataDisk** добавляет диск данных, который используется для хранения данных Active Directory, при этом параметр кэширования задан как None.
   * **New-AzureVM** создает новую облачную службу и новую виртуальную машину Azure в новой облачной службе.

7. Дождитесь полной подготовки к работе новой виртуальной машины и скачайте файл удаленного рабочего стола в рабочий каталог. Так как подготовка виртуальной машины Azure к работе занимает длительное время, цикл `while` продолжает опрашивать новую виртуальную машину до тех пор, пока она не станет готова к использованию.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

Сервер контроллера домена успешно подготовлен. Далее нужно настроить домен Active Directory на этом сервере контроллера домена. Не закрывайте окно PowerShell на локальном компьютере. Оно пригодится вам позже во время создания двух виртуальных машин SQL Server.

## <a name="configure-the-domain-controller"></a>Настройка контроллера домена
1. Подключитесь к серверу контроллера домена, запустив файл удаленного рабочего стола. Используйте имя администратора компьютера AzureAdmin и пароль **Contoso!000**, которые вы указали при создании новой виртуальной машины.
2. Откройте окно PowerShell с правами администратора.
3. Выполните следующую команду **DCPROMO.EXE**, чтобы настроить домен **corp.contoso.com** с каталогами данных на диске M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    После завершения команды виртуальная машина автоматически перезагружается.

4. Подключитесь к серверу контроллера домена еще раз, запустив файл удаленного рабочего стола. На этот раз войдите с именем **CORP\Administrator**.
5. Откройте окно Powershell в режиме администратора и импортируйте модуль Active Directory PowerShell с помощью следующей команды:

        Import-Module ActiveDirectory

6. Выполните следующие команды, чтобы добавить трех пользователей в домен.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** служит для настройки всего, что связано с экземплярами службы SQL Server, отказоустойчивым кластером и группой доступности. **CORP\SQLSvc1** и **CORP\SQLSvc2** используются в качестве учетных записей службы SQL Server для двух виртуальных машин SQL Server.
7. Затем выполните следующие команды, чтобы предоставить учетной записи **CORP\Install** разрешения для создания объектов компьютеров в домене.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Идентификатор GUID, указанный выше,— это GUID для типа объекта-компьютера. Для учетной записи **CORP\Install** требуются разрешения **Чтение всех свойств** и **Создание объектов компьютеров**. Они необходимы для создания объектов Active Directory для отказоустойчивого кластера. Разрешение **Чтение всех свойств** предоставлено учетной записи CORP\Install по умолчанию, поэтому предоставлять его явным образом не требуется. Дополнительные сведения о разрешениях, необходимых для создания отказоустойчивого кластера, см. в статье [Failover Cluster Step-by-Step Guide: Configuring Accounts in Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx) (Пошаговое руководство по отказоустойчивым кластерам. Настройка учетных записей в Active Directory).

    После завершения настройки Active Directory и объектов пользователей нужно создать две виртуальные машины SQL Server и присоединить их к этому домену.

## <a name="create-the-sql-server-vms"></a>Создание виртуальных машин SQL Server
1. Перейдите в окно PowerShell, открытое на локальном компьютере. Определите следующие дополнительные переменные:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    IP-адрес **10.10.0.4** обычно назначается первой виртуальной машине, созданной в подсети **10.10.0.0/16** вашей виртуальной сети Azure. Необходимо убедиться, что это адрес сервера контроллера домена. Для этого выполните команду **IPCONFIG**.
2. Выполните следующий набор команд, чтобы создать в отказоустойчивом кластере первую виртуальную машину с именем **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Обратите внимание на следующее относительно указанной выше команды:

   * **New-AzureVMConfig** создает конфигурацию виртуальной машины с заданным именем группы доступности. Последующие виртуальные машины будут созданы с тем же именем группы доступности, поэтому они присоединяются к той же группе доступности.
   * **Add-AzureProvisioningConfig** объединяет виртуальную машину с созданным доменом Active Directory.
   * **Set-AzureSubnet** размещает виртуальную машину в конечной подсети.
   * **New-AzureVM** создает новую облачную службу и новую виртуальную машину Azure в новой облачной службе. Параметр **DnsSettings** указывает, что для сервера DNS, который используется для серверов в новой облачной службе, задан IP-адрес **10.10.0.4**. Это IP-адрес сервера контроллера домена. Этот параметр необходим для активации новых виртуальных машин в облачной службе для успешного присоединения к домену Active Directory. Без этого параметра вам потребуется вручную настроить параметры IPv4 на виртуальной машине для использования сервера контроллера домена в качестве основного DNS-сервера после подготовки виртуальной машины и ее присоединения к домену Active Directory.
3. Выполните следующий набор команд, чтобы создать виртуальные машины SQL Server с именами **ContosoSQL1** и **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Обратите внимание на следующее относительно указанных выше команд:

   * **New-AzureVMConfig** использует то же имя группы доступности, что и сервер контроллера домена, и использует образ SQL Server 2012 Enterprise Edition с пакетом обновления 1 (SP1) из коллекции виртуальных машин. Кроме того, этот набор команд задает режим диска операционной системы на чтение кэша (запрет записи в кэш). Мы советуем переносить файлы баз данных на отдельный диск данных, подсоединенный к виртуальной машине, и настроить для него запрет на чтение кэша или запись в кэш. Однако наилучшая альтернатива — удалить кэширование записи на диске операционной системы, так как кэширование чтения с этого диска удалить нельзя.
   * **Add-AzureProvisioningConfig** объединяет виртуальную машину с созданным доменом Active Directory.
   * **Set-AzureSubnet** размещает виртуальную машину в конечной подсети.
   * **Add-AzureEndpoint** добавляет конечные точки доступа, чтобы клиентские приложения могли обращаться к экземплярам службы SQL Server через Интернет. Виртуальным машинам ContosoSQL1 и ContosoSQL2 назначаются различные порты.
   * **New-AzureVM** создает новую виртуальную машину SQL Server в той же облачной службе, что и ContosoQuorum. Если виртуальные машины должны находиться в одной группе доступности, то необходимо размещать их в одной облачной службе.
4. Дождитесь полной подготовки к работе каждой виртуальной машины и скачайте для каждой виртуальной машины файл удаленного рабочего стола в рабочий каталог. Цикл `for` проходит по трем новым виртуальным машинам и выполняет для каждой из них команды в фигурных скобках верхнего уровня.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Виртуальные машины SQL Server подготовлены и запущены, но они устанавливаются с SQL Server с использованием параметров по умолчанию.

## <a name="initialize-the-failover-cluster-vms"></a>Инициализация виртуальных машин отказоустойчивого кластера
В этом разделе вам необходимо изменить три сервера, которые будут использоваться в отказоустойчивом кластере и при установке SQL Server. В частности:

* Все серверы. Необходимо установить компонент **отказоустойчивой кластеризации**.
* Все серверы. Необходимо добавить учетную запись **CORP\Install** в качестве **администратора** виртуальной машины.
* Только ContosoSQL1 и ContosoSQL2. Необходимо добавить учетную запись **CORP\Install** в качестве роли **sysadmin** в базе данных по умолчанию.
* Только ContosoSQL1 и ContosoSQL2. Необходимо добавить учетную запись **NT AUTHORITY\System** в качестве имени входа со следующими разрешениями:

  * Изменение любой группы доступности
  * Соединение SQL
  * Просмотр состояния сервера
* Только ContosoSQL1 и ContosoSQL2. Протокол **TCP** уже включен на виртуальной машине SQL Server. Однако все еще требуется открыть брандмауэр для удаленного доступа к SQL Server.

Теперь все готово к запуску. Выполните следующие шаги, начиная с **ContosoQuorum**.

1. Подключитесь к **ContosoQuorum** , запустив файлы удаленного рабочего стола. Используйте имя администратора компьютера **AzureAdmin** и пароль **Contoso!000**, которые вы указали при создании виртуальной машины.
2. Убедитесь, что компьютеры успешно добавлены в домен **corp.contoso.com**.
3. Дождитесь, пока программа установки SQL Server не завершит автоматические задачи инициализации, прежде чем продолжить.
4. Откройте окно PowerShell с правами администратора.
5. Установите компонент отказоустойчивой кластеризации Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Добавьте **CORP\Install** в качестве локального администратора.

        net localgroup administrators "CORP\Install" /Add
7. Выйдите из ContosoQuorum. Теперь работа с этим сервером завершена.

        logoff.exe

Затем инициализируйте виртуальные машины **ContosoSQL1** и **ContosoSQL2**. Выполните следующие шаги, которые идентичны для обеих виртуальных машин SQL Server.

1. Подключитесь к двум виртуальным машинам SQL Server, запустив файлы удаленного рабочего стола. Используйте имя администратора компьютера **AzureAdmin** и пароль **Contoso!000**, которые вы указали при создании виртуальной машины.
2. Убедитесь, что компьютеры успешно добавлены в домен **corp.contoso.com**.
3. Дождитесь, пока программа установки SQL Server не завершит автоматические задачи инициализации, прежде чем продолжить.
4. Откройте окно PowerShell с правами администратора.
5. Установите компонент отказоустойчивой кластеризации Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Добавьте **CORP\Install** в качестве локального администратора.

        net localgroup administrators "CORP\Install" /Add
7. Импортируйте поставщика SQL Server PowerShell.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Добавьте **CORP\Install** в качестве роли sysadmin для экземпляра SQL Server по умолчанию.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Добавьте **NT AUTHORITY\System** в качестве имени входа с тремя описанными выше разрешениями.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Откройте брандмауэр для удаленного доступа к SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Выйдите из обеих виртуальных машин.

         logoff.exe

Теперь можно приступить к настройке группы доступности. Для выполнения всех действий с **ContosoSQL1** будет использоваться поставщик SQL Server PowerShell.

## <a name="configure-the-availability-group"></a>Настройка группы доступности
1. Снова подключитесь к **ContosoSQL1** , запустив файлы удаленного рабочего стола. Вместо входа с помощью учетной записи компьютера войдите с помощью учетной записи **CORP\Install**.
2. Откройте окно PowerShell с правами администратора.
3. Определите следующие переменные.

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Импортируйте поставщика SQL Server PowerShell.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Измените учетную запись службы SQL Server для ContosoSQL1 на CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Измените учетную запись службы SQL Server для ContosoSQL2 на CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Скачайте файл **CreateAzureFailoverCluster.ps1** из статьи [Create Failover Cluster for Always On Availability Groups in Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) (Создание отказоустойчивого кластера для групп доступности Always On на виртуальной машине Azure) в локальный рабочий каталог. Этот скрипт поможет создать рабочий отказоустойчивый кластер. Важные сведения о взаимодействии кластеров Windows с сетью Azure см. в статье [Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Измените рабочий каталог и создайте отказоустойчивый кластер с помощью скачанного сценария.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Включите группы доступности Always On для экземпляров SQL Server по умолчанию на виртуальных машинах **ContosoSQL1** и **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Создайте каталог резервной копии и предоставьте разрешения для учетных записей службы SQL Server. Этот каталог будет использоваться для подготовки базы данных на вторичной реплике.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Создайте базу данных в **ContosoSQL1** с именем **MyDB1**, создайте полный архив и архив журнала, а затем восстановите их на виртуальной машине **ContosoSQL2** с параметром **WITH NORECOVERY**.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Создайте конечные точки группы доступности на трех ВМ SQL Server и задайте соответствующие разрешения для конечных точек.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Создайте реплики доступности.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Теперь создайте группу доступности и добавьте в нее вторичную реплику.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Дополнительная информация
Решение SQL Server Always On на основе группы доступности в Azure успешно создано. Инструкции по настройке прослушивателя для этой группы доступности см. в статье [Настройка прослушивателя внутренней подсистемы балансировки нагрузки для группы доступности Always On в Azure](../classic/ps-sql-int-listener.md).

Дополнительные сведения об использовании SQL Server в Azure см. в статье [Общие сведения об SQL Server на виртуальных машинах Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
