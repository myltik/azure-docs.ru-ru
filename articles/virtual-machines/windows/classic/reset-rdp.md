---
title: Сброс пароля или конфигурации удаленного рабочего стола для виртуальной машины Windows в Azure | Документация Майкрософт
description: Узнайте, как с помощью портала Azure или Azure PowerShell сбросить пароль учетной записи или конфигурацию службы удаленного рабочего стола для виртуальной машины Windows, созданной с использованием классической модели развертывания.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 516cb740f9acad19dac77db0239341b42a2b27fe
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Сброс службы удаленного рабочего стола или ее пароля для входа в систему на виртуальной машине Windows, созданной с использованием классической модели развертывания
> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Вы также можете [выполнить эти шаги на виртуальных машинах, созданных на основе модели развертывания с помощью Resource Manager](../reset-rdp.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]


Если не удается подключиться к виртуальной машине Windows, можно сбросить пароль локального администратора или конфигурацию службы удаленного рабочего стола. Для сброса пароля можно использовать портал Azure или расширение VMAccess в Azure PowerShell.

## <a name="ways-to-reset-configuration-or-credentials"></a>Способы сброса конфигурации или учетных данных
В зависимости от ваших потребностей службы удаленных рабочих столов и учетные данные можно сбросить несколькими разными способами:

- [сброс с помощью портала Azure](#azure-portal);
- [сброс с помощью Azure PowerShell](#vmaccess-extension-and-powershell).

## <a name="azure-portal"></a>Портал Azure
Сброс службы удаленного рабочего стола можно выполнить с помощью [портала Azure](https://portal.azure.com). Чтобы развернуть меню портала, щелкните три полосы в левом верхнем углу и выберите **Виртуальные машины (классика)**.

![Выбор виртуальной машины Azure](./media/reset-rdp/Portal-Select-Classic-VM.png)

Выберите виртуальную машину Windows и щелкните **Сброс удаленной конфигурации (новый портал)**. Отобразится диалоговое окно сброса конфигурации удаленного рабочего стола.

![Страница "Сброс конфигурации удаленного рабочего стола"](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

Можно также сбросить имя и пароль учетной записи локального администратора. В виртуальной машине щелкните **Поддержка и устранение неполадок** > **Сбросить пароль**. Отобразится колонка сброса пароля.

![Страница "Сброс пароля"](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Введите новое имя пользователя и пароль, затем щелкните **Сохранить**.

## <a name="vmaccess-extension-and-powershell"></a>Расширение VMAccess и PowerShell
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

## <a name="next-steps"></a>Дополнительная информация
Если расширение доступа к виртуальной машине Azure не отвечает и сбросить пароль не удается, вы можете [сбросить локальный пароль Windows в автономном режиме](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Этот метод является более сложным процессом и требует подключения виртуального жесткого диска проблемной виртуальной машины к другой виртуальной машине. Сначала выполните действия, описанные в этой статье, и только потом попробуйте сбросить пароль в автономном режиме в качестве последнего средства.

[Расширения и компоненты виртуальных машин Azure](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Подключение к виртуальной машине Azure с помощью RDP или SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Устранение неполадок с подключением к удаленному рабочему столу виртуальной машины Windows в службе Azure](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

