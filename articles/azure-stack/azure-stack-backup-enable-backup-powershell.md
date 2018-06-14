---
title: Включение резервного копирования для Azure Stack с помощью PowerShell | Документация Майкрософт
description: Включите службу резервного копирования инфраструктуры с помощью Windows PowerShell для восстановления Azure Stack в случае сбоя.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 5fab656734d0984cf44a9fe1f29fd73530bd9aa8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259861"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Включение резервного копирования для Azure Stack с помощью PowerShell

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Включите службу "Резервное копирование инфраструктуры" с помощью Windows PowerShell, чтобы периодически создавать резервные копии:
 - внутренней службы идентификации и корневого сертификата;
 - планов, предложений и подписок пользователей;
 - секретов Key Vault;
 - ролей и политик RBAC пользователей.

Чтобы включить резервное копирование, начать его и получить сведения о результатах резервного копирования через конечную точку управления оператором, вы можете использовать командлеты PowerShell.

## <a name="prepare-powershell-environment"></a>Подготовка среды PowerShell

Инструкции по настройке среды PowerShell приведены в разделе [Установка PowerShell для Azure Stack](azure-stack-powershell-install.md).

## <a name="generate-a-new-encryption-key"></a>Создание ключа шифрования

Установите и настройте PowerShell для Azure Stack и средства Azure Stack.
 - См. статью [Начало работы с PowerShell в Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).
 - Ознакомьтесь с разделом [Скачивание средств Azure Stack из GitHub](azure-stack-powershell-download.md).

Откройте Windows PowerShell из командной строки с повышенными привилегиями и выполните следующие команды:
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
В этом же сеансе PowerShell выполните следующие команды:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> Для создания ключа необходимо использовать команду AzureStack-Tools.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Предоставление прав на совместное использование резервной копии, учетных данных и ключа шифрования для включения резервного копирования

В том же сеансе PowerShell измените следующий сценарий PowerShell, добавив переменные среды. Выполните обновленный сценарий, чтобы предоставить службе архивации инфраструктуры права на совместное использование учетных данных и ключа шифрования.

| Переменная        | ОПИСАНИЕ   |
|---              |---                                        |
| $username       | Введите **имя пользователя**, указав домен и имя пользователя для расположения общего диска. У этого пользователя должны быть достаточные права на чтение и запись файлов. Например, `Contoso\backupshareuser`. |
| $password       | Введите **пароль** пользователя. |
| $sharepath      | Введите путь к **расположению хранилища резервных копий**. В качестве пути к общей папке на отдельном устройстве нужно использовать UNC-строку. UNC-строка указывает расположение ресурсов, например общих файлов или устройств. Чтобы обеспечить доступность резервных копий, устройство должно находиться в отдельном расположении. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>Подтверждение параметров архивации

В этом же сеансе PowerShell выполните следующие команды:

   ```powershell
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
   ```

В результате должен отобразиться следующий блок выходных данных JSON:

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>Дополнительная информация

 - Дополнительные сведения о резервном копировании см. в статье [Резервное копирование для Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - См. дополнительные сведения о [подтверждении завершения резервного копирования на портале администрирования](azure-stack-backup-back-up-azure-stack.md ).
