---
title: "Передача учетных данных в Azure с помощью DSC | Документация Майкрософт"
description: "Общие сведения о безопасной передаче учетных данных для виртуальных машин Azure с помощью настройки требуемого состояния PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: acd768c0219ec23c0453a65c575faf5213d9c616
ms.lasthandoff: 03/31/2017


---
# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Передача учетных данных в обработчик расширения DSC Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

В этой статье описывается расширение настройки требуемого состояния для Azure. Обзор обработчика расширения DSC можно найти в статье [Введение в обработчик расширения настройки требуемого состояния Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

## <a name="passing-in-credentials"></a>Передача учетных данных
В рамках процесса настройки может потребоваться настройка учетных записей пользователей, доступ к службам или установка программы в пользовательском контексте. Чтобы выполнить эти действия, необходимо указать учетные данные. 

DSC позволяет использовать параметризованные конфигурации, в которых учетные данные передаются в конфигурацию и безопасно хранятся в MOF-файлах. Обработчик расширений Azure упрощает управление учетными данными, предоставляя автоматическое управление сертификатами. 

Рассмотрим следующий скрипт конфигурации DSC, который создает учетную запись локального пользователя с указанным паролем.

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

Важно включить *узел localhost* в конфигурацию. Если этот оператор отсутствует, то приведенные ниже шаги не будут работать, так как обработчик расширений специально ищет оператор node localhost. Важно также добавить приведение типа *[PsCredential]*, так как этот конкретный тип активирует в расширении шифрование учетных данных. 

Опубликуйте этот скрипт в хранилище BLOB-объектов:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Задайте расширение Azure DSC и укажите учетные данные:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"

$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>Защита учетных данных
При выполнении этого кода запрашиваются учетные данные. Указанные учетные данные кратковременно хранятся в памяти. При публикации с командлетом `Set-AzureVmDscExtension` учетные данные передаются по протоколу HTTPS к виртуальной машине, где Azure сохраняет их в зашифрованном виде на диске с использованием локального сертификата виртуальной машины. Затем они быстро расшифровываются в памяти и повторно шифруются для передачи в DSC.

Это поведение отличается от [использования безопасных конфигураций без обработчика расширений](https://msdn.microsoft.com/powershell/dsc/securemof). Среда Azure предоставляет способ безопасной передачи данных конфигурации с помощью сертификатов. При использовании обработчика расширений DSC нет необходимости указывать запись $CertificatePath либо $CertificateID или $Thumbprint в ConfigurationData.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об обработчике расширений DSC см. в статье [Общие сведения об обработчике расширения для настройки требуемого состояния в Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Изучите [шаблон Azure Resource Manager для расширения DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Для получения дополнительных сведений о DSC PowerShell [посетите центр документации PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Чтобы получить дополнительные функциональные возможности, которыми можно управлять с помощью DSC PowerShell, [найдите в коллекции PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) дополнительные материалы по DSC.


