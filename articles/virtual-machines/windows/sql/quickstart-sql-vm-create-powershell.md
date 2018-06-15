---
title: Создание виртуальной машины SQL Server под управлением Windows с помощью Azure PowerShell | Документация Майкрософт
description: Это руководство содержит инструкции по созданию виртуальной машины SQL Server 2017 под управлением Windows с помощью Azure PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: 228d2852d9554d378dc663e74460da7ab80d4b24
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602728"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Краткое руководство по созданию виртуальной машины SQL Server под управлением Windows с помощью Azure PowerShell

В этом кратком руководстве перечислены основные действия по созданию виртуальной машины SQL Server с помощью Azure PowerShell.

> [!TIP]
> В этом кратком руководстве приводятся инструкции по быстрой подготовке виртуальной машины SQL Server и подключению к ней. Дополнительные сведения о других способах создания виртуальных машин SQL Server с помощью Azure PowerShell см. в статье [Как создать виртуальные машины SQL Server с помощью Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

> [!TIP]
> Если у вас есть вопросы по виртуальным машинам SQL Server, см. раздел [часто задаваемых вопросов](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Оформление подписки Azure

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.


## <a id="powershell"></a> Установка Azure PowerShell

Для работы с этим кратким руководством требуется модуль Azure PowerShell 3.6 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-powershell"></a>Настройка PowerShell

1. Откройте PowerShell и установите доступ к учетной записи Azure, выполнив команду **Connect-AzureRmAccount**.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. Должно отобразиться окно входа, в котором необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

1. Определите переменную с уникальным именем группы ресурсов. Для упрощения дальнейших действий в рамках этого руководства это имя будет использоваться в остальных командах в качестве основы для других имен ресурсов.

   ```PowerShell
   $ResourceGroupName = "sqlvm1"
   ```

1. Определите расположение целевого региона Azure для всех ресурсов виртуальной машины.

   ```PowerShell
   $Location = "East US"
   ```

1. Создайте группу ресурсов.

   ```PowerShell
   New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Настройка параметров сети

1. Создайте виртуальную сеть, подсеть и общедоступный IP-адрес. Эти ресурсы используются для того, чтобы установить сетевое подключение к виртуальной машине и подключить ее к Интернету.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Создайте группу безопасности сети. Настройте правила, чтобы разрешить подключения к удаленному рабочему столу (RDP) и SQL Server.

   ```PowerShell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Создайте сетевой интерфейс.

   ```PowerShell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Создание виртуальной машины SQL

1. Определите учетные данные для входа на виртуальную машину. Используйте имя пользователя azureadmin. Перед выполнением команды измените пароль.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString 'Change.This!000' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Создайте объект конфигурации виртуальной машины, а затем создайте виртуальную машину. Следующая команда создает виртуальную машину SQL Server 2017 Developer Edition в ОС Windows Server 2016.

   ```PowerShell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzureRmVMConfig -VMName $VMName -VMSize Standard_DS13 | `
      Set-AzureRmVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzureRmVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzureRmVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Создание виртуальной машины занимает несколько минут.

## <a name="install-the-sql-iaas-agent"></a>Установка агента SQL IaaS

Для интеграции с порталом и получения компонентов виртуальной машины SQL необходимо установить [расширение агента SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Чтобы установить агент на новой виртуальной машине, после его создания выполните следующую команду:

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Подключение к виртуальной машине с помощью удаленного рабочего стола

1. Используйте следующую команду, чтобы получить общедоступный IP-адрес для новой виртуальной машины.

   ```PowerShell
   Get-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. После этого передайте возвращенный IP-адрес в качестве параметра командной строки для **mstsc**, чтобы запустить сеанс удаленного рабочего стола на новой виртуальной машине.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. При появлении запроса учетных данных выберите ввод учетных данных для другой учетной записи. Введите имя пользователя после обратной косой черты (например, `\azureadmin`) и пароль, заданные ранее в рамках этого краткого руководства.

## <a name="connect-to-sql-server"></a>Подключение к SQL Server

1. После входа в сеанс удаленного рабочего стола в меню "Пуск" запустите **SQL Server Management Studio 2017**.

1. В диалоговом окне **Подключение к серверу** оставьте значения по умолчанию. Имя сервера указано в качестве имени виртуальной машины. В параметрах проверки подлинности установлено значение **Проверка подлинности Windows**. Щелкните **Подключить**.

Теперь вы подключены к серверу SQL Server локально. Если вы хотите подключиться удаленно, необходимо [настроить подключение](virtual-machines-windows-sql-connect.md) на портале или вручную.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если не требуется, чтобы виртуальная машина работала постоянно, можно избежать ненужных затрат, останавливая ее, когда она не используется. С помощью следующей команды можно остановить виртуальную машину, оставив ее доступной для использования в будущем.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

А с помощью команды **Remove-AzureRmResourceGroup** можно удалить все ресурсы, связанные с виртуальной машиной, без возможности восстановления. Это также приведет к окончательному удалению самой виртуальной машины, поэтому указанную команду следует использовать с осторожностью.

## <a name="next-steps"></a>Дополнительная информация

В рамках этого краткого руководства вы создали виртуальную машину SQL Server 2017 с помощью Azure PowerShell. Чтобы узнать больше о том, как перенести данные на новый сервер SQL Server, ознакомьтесь со следующей статьей.

> [!div class="nextstepaction"]
> [Миграция базы данных SQL Server в экземпляр SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md)
