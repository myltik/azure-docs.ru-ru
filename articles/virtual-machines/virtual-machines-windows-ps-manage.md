<properties
	pageTitle="Управление виртуальными машинами с помощью диспетчера ресурсов и PowerShell | Microsoft Azure"
	description="Управление виртуальными машинами с помощью Azure Resource Manager и PowerShell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Управление виртуальными машинами Azure с помощью диспетчера ресурсов и PowerShell

## Установка Azure PowerShell
 
Сведения о том, как установить последнюю версию Azure PowerShell, выбрать нужную подписку и выполнить вход в учетную запись Azure, см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Задание переменных

Для всех команд в статье требуется имя группы ресурсов, в которой находится виртуальная машина, и имя управляемой виртуальной машины. Замените значение **$rgName** именем группы ресурсов, содержащей виртуальную машину. Замените значение **$vmName** именем виртуальной машины. Создайте переменные.

        $rgName = "resource group name"
        $vmName = "VM name"

## Отображение информации о виртуальной машине

Получите сведения о виртуальной машине.
  
        Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Результат буде выглядеть примерно так:

        ResourceGroupName        : rg1
        Id                       : /subscriptions/{subscription-id}/resourceGroups/
                                    rg1/providers/Microsoft.Compute/virtualMachines/vm1
        Name                     : vm1
        Type                     : Microsoft.Compute/virtualMachines
        Location                 : centralus
        Tags                     : {}
        AvailabilitySetReference : {
                                     "id": "/subscriptions/{subscription-id}/resourceGroups/
                                       rg1/providers/Microsoft.Compute/availabilitySets/av1"
                                   }
        Extensions               : []
        HardwareProfile          : {
                                     "vmSize": "Standard_A0"
                                   }
        InstanceView             : null
        NetworkProfile           : {
                                     "networkInterfaces": [
                                       {
                                         "properties.primary": null,
                                         "id": "/subscriptions/{subscription-id}/resourceGroups/
                                           rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                       }
                                     ]
                                   }
        OSProfile                : {
                                     "computerName": "vm1",
                                     "adminUsername": "myaccount1",
                                     "adminPassword": null,
                                     "customData": null,
                                     "windowsConfiguration": {
                                       "provisionVMAgent": true,
                                       "enableAutomaticUpdates": true,
                                       "timeZone": null,
                                       "additionalUnattendContents": [],
                                       "winRM": null
                                     },
                                     "linuxConfiguration": null,
                                     "secrets": []
                                   }
        Plan                     : null
        ProvisioningState        : Succeeded
        StorageProfile           : {
                                     "imageReference": {
                                       "publisher": "MicrosoftWindowsServer",
                                       "offer": "WindowsServer",
                                       "sku": "2012-R2-Datacenter",
                                       "version": "latest"
                                     },
                                     "osDisk": {
                                       "osType": "Windows",
                                       "encryptionSettings": null,
                                       "name": "osdisk",
                                       "vhd": {
                                         "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                       }
                                       "image": null,
                                       "caching": "ReadWrite",
                                       "createOption": "FromImage"
                                     }
                                     "dataDisks": [],
                                   }
        DataDiskNames            : {}
        NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                     rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## запуск виртуальной машины

Запустите виртуальную машину.

        Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Через несколько минут будут выданы примерно следующие данные:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Остановка виртуальной машины

Остановите виртуальную машину.

	    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Появится запрос на подтверждение:

        Virtual machine stopping operation
        This cmdlet will stop the specified virtual machine. Do you want to continue?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
        
Введите **Y**, чтобы остановить виртуальную машину.

Через несколько минут будут выданы примерно следующие данные:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Перезапуск виртуальной машины

Перезапустите виртуальную машину.

        $rgName = "resource group name"
        $vmName = "VM name"
        Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Результат буде выглядеть примерно так:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## удаление виртуальной машины

Удалите виртуальную машину.

        $rgName = "resource group name"
        $vmName = "VM name"
	    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [AZURE.NOTE] Чтобы пропустить запрос на подтверждение, используйте параметр **–Force**.

Если параметр -Force не используется, появляется запрос на подтверждение:

	    Virtual machine removal operation
	    This cmdlet will remove the specified virtual machine. Do you want to continue?
	    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Результат буде выглядеть примерно так:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Обновление виртуальной машины

В этом примере показано, как изменить размер виртуальной машины.
        
        $vmSize = "Standard_A1"
        $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
        $vm.HardwareProfile.vmSize = $vmSize
        Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
    See [Sizes for virtual machines in Azure](virtual-machines-windows-sizes.md) for a list of available sizes for a virtual machine.

Результат буде выглядеть примерно так:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Дальнейшие действия

При наличии проблем с развертыванием обратитесь к статье [Устранение неполадок развертываний для групп ресурсов с помощью портала Azure](../resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0420_2016-->