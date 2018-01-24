---
title: "Смена секретов в Azure Stack | Документация Майкрософт"
description: "Дополнительные сведения о смене секретов в Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: e2e9d93af3889714ade1d0364a6f747c184e6d75
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Смена секретов в Azure Stack

*Область применения: интегрированные системы Azure Stack*

Следует регулярно обновлять пароли для компонентов Azure Stack.

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>Обновление паролей для контроллера управления основной платой

Контроллеры управления основной платой отслеживают физическое состояние серверов. Спецификации и инструкции по обновлению паролей контроллеров управления основной платой различаются в зависимости от поставщика вычислительной техники.

1. Обновите контроллер управления основной платой на физических серверах Azure Stack в соответствии с инструкциями поставщика. Пароли для всех контроллеров в среде должны совпадать.
2. Откройте привилегированную конечную точку в сеансах Azure Stack. См. инструкции по [использованию привилегированной конечной точки в Azure Stack](azure-stack-privileged-endpoint.md).
3. После изменения запроса PowerShell на **[IP-адрес или имя виртуальной машины ERCS]: PS>** или на **[azs-ercs01]: PS>** (в зависимости от среды) выполните `Set-BmcPassword`, запустив `invoke-command`. Передайте переменную сеанса привилегированной конечной точки в качестве параметра. Например: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```
    
    Также можно использовать статическую версию PowerShell с паролями в качестве строк кода:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о [состоянии безопасности инфраструктуры Azure Stack](azure-stack-security-foundations.md).
