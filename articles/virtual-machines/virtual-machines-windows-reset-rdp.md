---
title: "Сброс пароля или конфигурации удаленного рабочего стола для виртуальной машины Windows | Документация Майкрософт"
description: "Узнайте, как сбросить пароль учетной записи или конфигурацию службы удаленного рабочего стола для виртуальной машины Windows с помощью портала Azure или Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 6700ea97bea02d68329b923f8715d84e5df1de33


---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Сброс службы удаленных рабочих столов или ее пароля для входа в систему на виртуальной машине под управлением Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Если не удается подключиться к виртуальной машине Windows, можно сбросить пароль локального администратора или конфигурацию службы удаленного рабочего стола. Для сброса пароля можно использовать портал Azure или расширение VMAccess в Azure PowerShell. Если вы используете PowerShell, убедитесь, что на рабочем компьютере установлен последний модуль PowerShell и вы вошли в подписку Azure. Дополнительные сведения см. в разделе [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!TIP]
> Чтобы узнать, какая версия PowerShell установлена, используйте приведенную ниже команду.
>
> `Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`


## <a name="ways-to-reset-configuration-or-credentials"></a>Способы сброса конфигурации или учетных данных
Можно сбросить службы удаленных рабочих столов и учетные данные несколькими разными способами, в зависимости от ваших потребностей. Для виртуальных машин, созданных с использованием модели развертывания с помощью Resource Manager:

- [сброс с помощью портала Azure](#azure-portal---resource-manager);
- [сброс с помощью Azure PowerShell](#vmaccess-extension-and-powershell---resource-manager).

Для виртуальных машин, созданных с использованием классической модели развертывания:

- [сброс с помощью портала Azure](#azure-portal---classic);
- [сброс с помощью Azure PowerShell](#vmaccess-extension-and-powershell---classic).

## <a name="azure-portal---resource-manager"></a>Портал Azure — Resource Manager
Выберите свою виртуальную машину, выбрав **Обзор** > **Виртуальные машины** > *ваша виртуальная машина Windows* > **Все параметры** > **Сбросить пароль**. Отобразится колонка сброса пароля.

![Страница "Сброс пароля"](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Введите имя пользователя и новый пароль, а затем щелкните **Сохранить**. Снова попробуйте подключиться к виртуальной машине.


## <a name="vmaccess-extension-and-powershell---resource-manager"></a>Расширение VMAccess и PowerShell — Resource Manager
Убедитесь, что у вас установлен компонент Azure PowerShell 1.0 или более поздняя версия и вы вошли в систему под своей учетной записью с помощью командлета `Login-AzureRmAccount` .

### <a name="reset-the-local-administrator-account-password"></a>**Сброс пароля учетной записи локального администратора**
Чтобы сбросить пароль и имя пользователя администратора, выполните команду PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) .

Создайте учетные данные для учетной записи локального администратора с помощью следующей команды:

```powershell
$cred=Get-Credential
```

Если вы укажете другое имя для учетной записи, следующая команда расширения VMAccess переименует учетную запись локального администратора, назначит пароль этой учетной записи и создаст событие закрытия удаленного рабочего стола. Если учетная запись локального администратора отключена, расширение VMAccess включит ее.

Используйте расширение VMAccess, чтобы задать новые учетные данные, следующим образом:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Замените `myResourceGroup`, `myVM`, `myVMAccess` и расположение в соответствии с параметрами своей установки.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Сброс конфигурации службы удаленных рабочих столов**
Удаленный доступ к виртуальной машине можно сбросить с помощью командлетов [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) и [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx), как показано ниже. (Замените `myResourceGroup`, `myVM`, `myVMAccess` и расположение своими собственными значениями.)

```powershell
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
    -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"
```

Или: 

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0
```

> [!TIP]
> Обе команды добавляют новый именованный агент доступа к виртуальной машине. В любой момент на виртуальной машине может быть только один агент доступа. Чтобы последовательно задать свойства агента доступа, удалите настроенный ранее агент доступа с помощью `Remove-AzureRmVMAccessExtension` или `Remove-AzureRmVMExtension`. 
>
> Если вы используете Azure PowerShell версии 1.2.2 или более поздней версии, выполнять этот шаг не требуется при использовании `Set-AzureRmVMExtension` с параметром `-ForceRerun`. Если вы используете параметр `-ForceRerun`, укажите для агента доступа имя, настроенное в предыдущей команде.

Если по-прежнему не удается удаленно подключиться к виртуальной машине, ознакомьтесь со статьей [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="azure-portal---classic"></a>Портал Azure — классическая модель
Для сброса службы удаленных рабочих столов на виртуальных машинах, созданных с помощью классической модели развертывания, можно использовать [портал Azure](https://portal.azure.com) . Последовательно выберите **Обзор** > **Виртуальные машины (классические)** > *ваша виртуальная машина Windows* > **Сброс удаленного доступа**. Откроется следующая страница.

![Страница "Сброс конфигурации удаленного рабочего стола"](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Можно также попробовать сбросить имя и пароль учетной записи локального администратора. Последовательно выберите **Обзор** > **Виртуальные машины (классические)** > *ваша виртуальная машина Windows* > **Все параметры** > **Сбросить пароль**. Откроется следующая страница.

![Страница "Сброс пароля"](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Введите новое имя пользователя и пароль, затем щелкните **Сохранить**.

## <a name="vmaccess-extension-and-powershell---classic"></a>Расширение VMAccess и PowerShell — классическая модель
Убедитесь, что агент ВМ установлен на виртуальной машине. Для использования расширения VMAccess его не обязательно нужно устанавливать, если доступен агент ВМ. Убедитесь, что агент VM уже установлен на виртуальной машине, используя следующую команду. (Замените myCloudService и myVM именами облачной службы и виртуальной машины соответственно. Чтобы найти эти имена, выполните команду `Get-AzureVM` без параметров.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Если команда **write-host** отображает значение **True**, агент ВМ установлен. Если она отображает значение **False**, соответствующие инструкции и ссылку для скачивания можно найти в записи блога Azure [Агент виртуальной машины и расширения — часть 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) .

Если вы создали виртуальную машину с помощью портала, проверьте, возвращает ли `$vm.GetInstance().ProvisionGuestAgent` значение **True**. Если нет, укажите это значение с помощью следующей команды:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Если выполнить эту команду, то во время выполнения команды **Set-AzureVMExtension** на следующих шагах не появится сообщение о том, что перед настройкой расширения виртуальной машины IaaS требуется включить гостевой агент подготовки в объекте виртуальной машины.

### <a name="reset-the-local-administrator-account-password"></a>**Сброс пароля учетной записи локального администратора**
Создайте учетные данные для входа с именем учетной записи текущего локального администратора и новым паролем, а затем выполните команду `Set-AzureVMAccessExtension` .

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Если вы укажете другое имя для учетной записи, расширение VMAccess переименует учетную запись локального администратора, назначит пароль этой учетной записи и закроет удаленный рабочий стол. Если учетная запись локального администратора отключена, расширение VMAccess включит ее.

Эти команды также сбрасывают конфигурацию службы удаленных рабочих столов.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Сброс конфигурации службы удаленных рабочих столов**
Чтобы сбросить конфигурацию службы удаленных рабочих столов, выполните следующую команду.

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

Расширение VMAccess запустит эти две команды на виртуальной машине:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Эта команда включает встроенную группу брандмауэра Windows, которая разрешает входящий трафик удаленного рабочего стола через TCP-порт 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Эта команда задает значение реестра fDenyTSConnections равным 0, включая подключения удаленного рабочего стола.

## <a name="next-steps"></a>Дальнейшие действия
Если расширение доступа к виртуальной машине Azure не отвечает и сбросить пароль не удается, вы можете [сбросить локальный пароль Windows в автономном режиме](virtual-machines-windows-reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Этот метод является более сложным процессом и требует подключения виртуального жесткого диска проблемной виртуальной машины к другой виртуальной машине. Сначала выполните действия, описанные в этой статье, и только потом попробуйте сбросить пароль в автономном режиме в качестве последнего средства.

[Расширения и компоненты виртуальных машин Azure](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Подключение к виртуальной машине Azure с помощью RDP или SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Устранение неполадок с подключением к удаленному рабочему столу виртуальной машины Windows в службе Azure](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Dec16_HO2-->


