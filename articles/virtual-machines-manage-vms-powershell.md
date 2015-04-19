<properties
   pageTitle="manage-vms-azure-powershell"
   description="Управление виртуальными машинами с помощью Azure PowerShell"
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="kasing"/>

# Управление виртуальными машинами с помощью Azure PowerShell

Перед началом работы необходимо установить Azure PowerShell. Соответствующие указания см. в статье [Установка и настройка Azure PowerShell](install-configure-powershell.md)

## Получение образа

Прежде чем приступить к созданию виртуальной машины, выберите **нужный образ**. Для вызова списка всех образов, доступных в Azure,

      Get-AzureVMImage

воспользуйтесь приведенным ниже командлетом. Из-за большого размера этого списка в нем может быть сложно найти нужный образ. В приведенном ниже примере используются другие командлеты PowerShell, которые позволяют вызвать список только тех образов, которые созданы на основе **Windows Server 2012 R2 Datacenter**. Кроме того, чтобы получить последнюю версию образа, можно указать значение [-1] для параметра возвращаемого массива образов.

    $img = (Get-AzureVMImage | Select -Property ImageName, Label | where {$_.Label -like '*Windows Server 2012 R2 Datacenter*'})[-1]

## Создание виртуальной машины

Чтобы приступить к созданию виртуальной машины, воспользуйтесь командлетом **New-AzureVMConfig**. Укажите **имя** виртуальной машины, ее **размер**, а также нужный **образ**. Этот командлет создает локальный объект виртуальной машины **$myVM**, который позднее можно будет изменить с помощью других командлетов Azure PowerShell, описанных в этом руководстве.

      $myVM = New-AzureVMConfig -Name "testvm" -InstanceSize "Small" -ImageName $img.ImageName

Далее нужно указать **имя пользователя** и **пароль** для виртуальной машины. Это можно сделать с помощью командлета **Add-AzureProvisioningConfig**. На этом этапе необходимо указать ОС, которая будет использоваться на виртуальной машине. Обратите внимание, что изменения вносятся в локальный объект **$myVM**.

    $user = "azureuser"
    $pass = "&Azure1^Pass@"
    $myVM = Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass

Теперь виртуальную машину можно разместить в Azure. Для этого нужно использовать командлет **New-AzureVM**.

> [AZURE.NOTE] Перед созданием виртуальной машины вам понадобится настроить облачную службу. Это можно сделать двумя способами.
* Используйте для создания облачной службы командлет New-AzureService. В этом случае нужно будет проверить, чтобы указанное в командлете New-AzureVM расположение совпадало с расположением облачной службы, иначе его выполнение будет невозможно.
* Используйте командлет New-AzureVM для автоматического создания облачной службы. В этом случае необходимо указать уникальное имя службы, иначе выполнение командлета будет невозможно.

    New-AzureVM -ServiceName "mytestserv" -VMs $myVM -Location "West US"

**ДОПОЛНИТЕЛЬНО**

Вы можете воспользоваться другими командлетами, такими как **Add-AzureDataDisk** или **Add-AzureEndpoint**, чтобы настроить дополнительные параметры виртуальной машины.

## Получение доступа к виртуальной машине
Создав виртуальную машину в Azure, вы наверняка захотите узнать, как она работает. Это можно сделать с помощью командлета **Get-AzureVM**, как показано ниже.

    Get-AzureVM -ServiceName "mytestserv" -Name "testvm"


## Дальнейшие действия
[Подключение к виртуальной машине Azure с помощью RDP или SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx)<br>
[Часто задаваемые вопросы о виртуальных машинах Azure](https://msdn.microsoft.com/library/azure/dn683781.aspx)

<!--HONumber=47-->
