<properties
	pageTitle="Сброс пароля или удаленного рабочего стола на виртуальной машине Windows | Microsoft Azure"
	description="Сброс пароля администратора или служб удаленных рабочих столов на виртуальной машине под управлением Windows, созданной на основе модели развертывания диспетчера ресурсов."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="dkshir"/>

# Сброс службы удаленных рабочих столов или ее пароля для входа в систему на виртуальной машине Azure под управлением Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].


Вы не можете подключиться к виртуальной машине Windows, так как забыли пароль или возникла проблема с конфигурацией службы удаленных рабочих столов? В этой статье описано, как сбросить пароль локального администратора или конфигурацию службы удаленных рабочих столов.

В зависимости от модели развертывания виртуальной машины можно использовать портал или расширение VM Access в Azure PowerShell. При использовании Azure PowerShell убедитесь, что на рабочем компьютере установлен последний модуль Azure PowerShell и вы вошли в подписку Azure. Дополнительные сведения см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md).


> [AZURE.TIP] Чтобы узнать, какая версия Azure PowerShell установлена, используйте команду `Get-Module azure | format-table version`.


## Виртуальные машины с Windows в классической модели развертывания.

### Портал Azure

В случае виртуальных машин, созданных с помощью классической модели развертывания, можно использовать [портал Azure](https://portal.azure.com) для сброса службы удаленных рабочих столов. Последовательно выберите пункты **Обзор** > **Виртуальные машины (классические)** > *ваша виртуальная машина с Windows* > **Сброс удаленного доступа**. Откроется следующая страница.


![](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Можно также попробовать сбросить имя и пароль учетной записи локального администратора. Последовательно выберите пункты **Обзор** > **Виртуальные машины (классические)** > *Ваша виртуальная машина с Windows* > **Все настройки** > **Сбросить пароль**. Откроется следующая страница.

![](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Щелкните **Сохранить**, введя новое имя пользователя и пароль.

### Расширение VMAccess и PowerShell

Убедитесь, что агент ВМ установлен на виртуальной машине. Для использования расширения VMAccess его не обязательно нужно устанавливать, если доступен агент ВМ. Убедитесь, что агент VM уже установлен на виртуальной машине, используя следующую команду. Замените "myCloudService" и "myVM" на имена облачной службы и виртуальной машины соответственно. Их можно найти, выполнив `Get-AzureVM` без параметров.

	$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
	write-host $vm.VM.ProvisionGuestAgent

Если команда **write-host** отображает значение **True**, агент ВМ установлен. Если она отображает значение **False**, соответствующие инструкции и ссылку для скачивания можно найти в записи блога Azure [Агент виртуальной машины и расширения — часть 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409).

Если вы создали виртуальную машину с помощью портала, проверьте, возвращает ли `$vm.GetInstance().ProvisionGuestAgent` значение **True**. В противном случае установите его с помощью этой команды:

	$vm.GetInstance().ProvisionGuestAgent = $true

Эта команда будет предотвращать возникновение ошибки о том, что перед настройкой расширения виртуальных машин IaaS требуется включить гостевой агент подготовки в объекте виртуальной машины, при выполнении команды **Set-AzureVMExtension** в следующих разделах.

#### **Сброс пароля учетной записи локального администратора**

Создайте учетные данные для входа с именем учетной записи текущего локального администратора и новым паролем, а затем выполните `Set-AzureVMAccessExtension` следующим образом.

	$cred=Get-Credential
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Если вы укажете новое имя для учетной записи, расширение VMAccess переименует учетную запись локального администратора, назначит пароль этой учетной записи и закроет удаленный рабочий стол. Если учетная запись локального администратора отключена, расширение VMAccess включит ее.

Эти команды также сбрасывают конфигурацию службы удаленных рабочих столов.

#### **Сброс конфигурации службы удаленных рабочих столов**

Чтобы сбросить конфигурацию службы удаленных рабочих столов, выполните следующую команду.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

Расширение VMAccess запустит эти две команды в виртуальной машине:

А. `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Эта команда включает встроенную группу брандмауэра Windows, которая разрешает входящий трафик удаленного рабочего стола через TCP-порт 3389.

Б. `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Эта команда задает значение реестра fDenyTSConnections равным 0, включая подключения удаленного рабочего стола.


## Виртуальные машины с Windows в модели развертывания Resource Manager

Портал Azure сейчас не поддерживает сброс удаленного доступа или учетных данных для входа в случае виртуальных машин, созданных с помощью Resource Manager.


### Расширение VMAccess и PowerShell

Убедитесь, что у вас есть Azure PowerShell 1.0 или более поздняя версия и вы вошли в систему по своей учетной записи с помощью командлета `Login-AzureRmAccount`.

#### **Сброс пароля учетной записи локального администратора**

Сбросить пароль и имя пользователя администратора можно с помощью команды PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx).

Создайте учетные данные для учетной записи локального администратора с помощью следующей команды:

	$cred=Get-Credential

Если вы укажете имя, отличное от имени текущей учетной записи, расширение VMAccess переименует учетную запись локального администратора, назначит ей пароль и выйдет из удаленного рабочего стола. Если учетная запись локального администратора отключена, расширение VMAccess включит ее.
	
Используйте расширение VMAccess, чтобы задать новые учетные данные, следующим образом:

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password


Замените `myRG`, `myVM`, `myVMAccess` и расположение в соответствии с параметрами своей установки.


#### **Сброс конфигурации службы удаленных рабочих столов**

Удаленный доступ к виртуальной машине можно сбросить с помощью командлетов [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) и Set-AzureRmVMAccessExtension, как показано ниже. Замените `myRG`, `myVM`, `myVMAccess` и расположение на свои собственные значения.

	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus

ИЛИ<br>

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus

	
> [AZURE.TIP] Как `Set-AzureRmVMAccessExtension`, так и `Set-AzureRmVMExtension` добавляет новый именованный агент доступа к виртуальной машине. В любой момент в виртуальной машине может быть только один агент доступа. Чтобы последовательно задать свойства агента доступа, удалите настроенный ранее агент доступа с помощью `Remove-AzureRmVMAccessExtension` или `Remove-AzureRmVMExtension`. Начиная с Azure PowerShell версии 1.2.2, выполнять этот шаг не требуется при использовании `Set-AzureRmVMExtension` с параметром `-ForceRerun`. Укажите для агента доступа имя, настроенное в предыдущей команде, если используете параметр `-ForceRerun`.


Если по-прежнему не удается удаленно подключиться к виртуальной машине, см. дополнительные шаги в разделе [Устранение неполадок подключений к удаленному рабочему столу в виртуальной машине Azure под управлением Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).


## Дополнительные ресурсы

[Расширения и компоненты виртуальных машин Azure](virtual-machines-windows-extensions-features.md)

[Подключение к виртуальной машине Azure с помощью RDP или SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Устранение неполадок с подключением к удаленному рабочему столу виртуальной машины Windows в службе Azure](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0323_2016-->