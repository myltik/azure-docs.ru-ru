---
title: Включение подключения к удаленному рабочему столу для роли в облачных службах Azure с помощью PowerShell
description: Как настроить приложение облачной службы Azure для подключений к удаленному рабочему столу с помощью PowerShell
services: cloud-services
documentationcenter: ''
author: thraka
manager: timlt
editor: ''
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 84fe7ba418399562b6e36ed009c5e6e47fbe24da
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874146"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Включение подключения к удаленному рабочему столу для роли в облачных службах Azure с помощью PowerShell

> [!div class="op_single_selector"]
> * [портал Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

С помощью удаленного рабочего стола обеспечивается доступ к рабочему столу экземпляра, работающего в Azure. Подключение к удаленному рабочему столу позволяет диагностировать и устранять неполадки выполняющегося приложения.

В этой статье описывается включение удаленного рабочего стола для ролей облачной службы с помощью PowerShell. Сведения о компонентах, которые потребуются для выполнения инструкций в этой статье, см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). В PowerShell используется расширение удаленного рабочего стола, поэтому удаленный рабочий стол можно включить даже после развертывания приложения.

## <a name="configure-remote-desktop-from-powershell"></a>Настройка удаленного рабочего стола с помощью PowerShell

Командлет [Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) позволяет включить удаленный рабочий стол для всех или некоторых ролей в развернутой облачной службе. Он позволяет указать имя и пароль пользователя удаленного рабочего стола с помощью параметра *Credential* , который принимает объект PSCredential.

Если вы используете PowerShell в интерактивном режиме, то можете задать объект PSCredential, вызвав командлет [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) .

```
$remoteusercredentials = Get-Credential
```

Эта команда открывает диалоговое окно, в котором вы сможете в защищенном режиме указать имя и пароль удаленного пользователя.

Так как PowerShell используется в сценариях автоматизации, объект **PSCredential** можно настроить так, чтобы участие пользователя не требовалось. Сначала необходимо задать надежный пароль. Для начала нужно придумать текстовый пароль и преобразовать его в защищенную строку с помощью командлета [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Далее следует преобразовать защищенную строку в зашифрованную стандартную строку, используя командлет [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). После этого вы можете сохранить зашифрованную стандартную строку в файл с помощью командлета [Set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Можно также создать файл с надежным паролем, чтобы не нужно было каждый раз вводить пароль. Кроме того, файл с надежным паролем безопаснее, чем обычный текстовый файл. Для создания файла надежного пароля используйте эту команду PowerShell:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> При выборе пароля убедитесь, что соблюдены [требования к сложности](https://technet.microsoft.com/library/cc786468.aspx).

Чтобы создать объект учетных данных из файла с надежным паролем, необходимо считать содержимое файла и преобразовать его обратно в защищенную строку с помощью командлета [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

Командлет [Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) принимает также параметр *Expiration* , который указывает значение **DateTime** , определяющее дату и время истечения срока действия учетной записи пользователя. Например, можно задать срок действия учетной записи, который истечет через несколько дней.

В этом примере показано, как установить расширение удаленного рабочего стола для облачной службы с помощью PowerShell:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
При необходимости вы также можете указать слот развертывания и роли, для которых необходимо включить удаленный рабочий стол. Если эти параметры не указаны, командлет по умолчанию включит использование удаленного рабочего стола для всех ролей в слоте развертывания **рабочей среды** .

Расширение удаленного рабочего стола привязывается к развернутой службе. В случае создания нового развертывания службы потребуется включить использование удаленного рабочего стола для этого развертывания. Если необходимо, чтобы использование удаленного рабочего стола включалось всегда, рассмотрите возможность интеграции соответствующих сценариев PowerShell в рабочий процесс развертывания.

## <a name="remote-desktop-into-a-role-instance"></a>Подключение к экземпляру роли по протоколу удаленного рабочего стола

Командлет [Get-AzureRemoteDesktopFile](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) можно использовать, чтобы добавить удаленный рабочий стол для определенного экземпляра роли облачной службы. Можно использовать параметр *LocalPath* , чтобы скачать RDP-файл на локальный компьютер. Вы можете также использовать параметр *Launch* , чтобы открыть диалоговое окно "Подключение к удаленному рабочему столу" для доступа к экземпляру роли облачной службы.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Убедитесь, что расширение удаленного рабочего стола включено в службе.

Командлет [Get-AzureServiceRemoteDesktopExtension](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) показывает, включен ли удаленный рабочий стол в развернутой службе. Он возвращает имя пользователя удаленного рабочего стола и роли, для которых включено расширение удаленного рабочего стола. По умолчанию это выполняется в слоте развертывания, и вы можете выбрать слот промежуточного развертывания.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Удаление расширения удаленного рабочего стола из службы

Если вы уже включили расширение удаленного рабочего стола в развернутой службе и хотите обновить параметры удаленного рабочего стола, то сначала удалите расширение удаленного рабочего стола. Затем снова включите его с новыми параметрами. Например, если вы хотите задать новый пароль для учетной записи удаленного пользователя или ее срок действия истек. Данное действие необходимо только для существующих развертываний, в которых включено расширение удаленного рабочего стола. К новым развертываниям можно непосредственно применить расширение.

Чтобы удалить расширение удаленного рабочего стола из развернутой службы, используйте командлет [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) . При необходимости можно также указать слот развертывания и роль, для которой необходимо удалить удаленный рабочий стол.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Чтобы полностью удалить конфигурацию расширения, выполните командлет *remove* с параметром **UninstallConfiguration** .
>
> Параметр **UninstallConfiguration** удаляет все конфигурации расширения, примененные к службе. Каждая конфигурация расширения связана с конфигурацией службы. Вызов командлета *remove* без параметра **UninstallConfiguration** разорвет связь <mark>развертывания</mark> с конфигурацией расширения, фактически удаляя это расширение. Однако конфигурация расширения будет по-прежнему связана со службой.

## <a name="additional-resources"></a>Дополнительные ресурсы

[Настройка облачных служб](cloud-services-how-to-configure-portal.md)
