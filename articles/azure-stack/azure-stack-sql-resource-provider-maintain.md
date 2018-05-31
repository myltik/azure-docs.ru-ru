---
title: Использование баз данных SQL в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть базы данных SQL в качестве службы для Azure Stack, и изучите простую процедуру развертывания адаптера поставщика ресурсов SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 53436d131672622ae1a72a1bb84d5aa83fdbdc0c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207937"
---
# <a name="maintenance-operations"></a>Операции обслуживания 
Поставщик ресурсов SQL представляет собой заблокированную виртуальную машину. Обновление безопасности виртуальной машины поставщика ресурсов может быть выполнено через конечную точку PowerShell Just Enough Administration (JEA) _DBAdapterMaintenance_. Для упрощения выполнения этих операций сценарий предоставляется вместе с пакетом установки поставщика ресурсов.

## <a name="patching-and-updating"></a>Установка исправлений и обновлений
Поставщик ресурсов SQL не обслуживается как часть Azure Stack, так как это дополнительный компонент. Корпорация Майкрософт будет предоставлять обновления поставщика ресурсов SQL при необходимости. Экземпляр поставщика ресурсов SQL создается на виртуальной машине _пользователя_ в рамках подписки поставщика по умолчанию. Таким образом он необходим для предоставления исправлений Windows, защиты от вирусов и т. д. Пакеты обновления Windows, предоставляемые в рамках цикла исправлений и обновлений, можно использовать для применения обновлений к виртуальной машине Windows. При выпуске обновленного адаптера предоставляется скрипт для применения обновления. Этот скрипт создает виртуальную машину поставщика ресурсов и переносит любое имеющееся состояние.

 ## <a name="backuprestoredisaster-recovery"></a>Резервное копирование, восстановление и аварийное восстановление
 Поставщик ресурсов SQL не участвует в процессе резервного копирования и аварийного восстановления Azure Stack, так как это дополнительный компонент. Для упрощения будут предоставлены скрипты:
- Архивация необходимых сведений о состоянии (хранимых в учетной записи хранения Azure Stack).
- Восстановление поставщика ресурсов в случае полного восстановления стека становится необходимостью.
Перед восстановлением поставщика ресурсов требуется восстановить серверы базы данных (если это необходимо).

## <a name="updating-sql-credentials"></a>Обновление учетных данных SQL
Вы несете ответственность за создание и обслуживание учетных записей системного администратора на серверах SQL. Поставщику ресурсов нужна учетная запись с этими привилегиями для управления базами данных от имени пользователей. Доступ к данным в этих базах данных не требуется. При необходимости обновить пароли на серверах SQL Server можно использовать функцию обновления интерфейса администратора поставщика ресурсов, чтобы изменить сохраненный пароль, используемый поставщиком ресурсов. Эти пароли сохраняются в Key Vault в экземпляре Azure Stack.

Чтобы изменить параметры, щелкните **Обзор** &gt; **ADMINISTRATIVE RESOURCES** (Административные ресурсы) &gt; **SQL Hosting Servers** (Серверы размещения SQL) &gt; **Имена входа SQL** и выберите имя входа. Сначала необходимо внести изменения в экземпляр SQL (и любые реплики, если необходимо). На панели **Параметры** щелкните **Пароль**.

![Обновление пароля администратора](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="update-the-virtual-machine-operating-system"></a>Обновление ОС виртуальной машины
Существует несколько способов обновить виртуальную машину с Windows Server:
* Установка пакета последней версии поставщика ресурсов с помощью текущего исправленного образа основных компонентов Windows Server 2016.
* Установка пакета обновления Windows во время установки или обновления поставщика ресурсов.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Обновление определений Защитника Windows на виртуальной машине
Чтобы обновить определения Защитника, выполните следующие действия.

1. Загрузите обновление определений Защитника Windows на странице [Definition updates for Windows Defender Antivirus and other Microsoft antimalware](https://www.microsoft.com/en-us/wdsi/definitions) (Обновления определений для антивирусной программы "Защитник Windows" и других антивирусных программ корпорации Майкрософт).

    На этой странице из раздела "Загрузка и установка определений вручную" загрузите 64-битный файл антивирусной программы "Защитник Windows" для Windows 10 и Windows 8.1. 
    
    Прямая ссылка: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Создайте сеанс PowerShell для конечной точки обслуживания виртуальной машины для адаптера поставщика ресурсов SQL.
3. Скопируйте файл обновления определений на компьютер адаптера базы данных, используя сеанс обслуживания конечной точки.
4. Во время сеанса обслуживания PowerShell вызовите команду _Update-DBAdapterWindowsDefenderDefinitions_.
5. После установки рекомендуем удалить использованный файл обновления определений. Его можно удалить во время сеанса обслуживания с помощью команды _Remove-ItemOnUserDrive)_.


Ниже приведен пример сценария для обновления определений Защитника (замените адрес или имя виртуальной машины на фактическое значение).

```powershell
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


## <a name="collect-diagnostic-logs"></a>Сбор данных журналов диагностики
Поставщик ресурсов SQL представляет собой заблокированную виртуальную машину. При необходимости сбора данных журнала с виртуальной машины предоставляется конечная точка PowerShell Just Enough Administration (JEA) _DBAdapterDiagnostics_. С помощью этой конечной точки можно выполнить две команды:

* Get-AzsDBAdapterLog: готовит ZIP-пакет, содержащий журналы диагностики RP, и помещает его в пользовательский диск для сеанса. Эту команду можно вызвать без параметров. Она будет собирать данные журнала за последние четыре часа.
* Remove-AzsDBAdapterLog: очищает имеющиеся пакеты журналов на виртуальной машине поставщика ресурсов.

Во время развертывания поставщика ресурсов или обновления подключения к конечной точке диагностики для извлечения журналов поставщика ресурсов создается учетная запись пользователя с именем _dbadapterdiag_. Пароль этой учетной записи совпадает с паролем, предоставленным для учетной записи локального администратора во время развертывания или обновления.

Чтобы использовать эти команды, необходимо создать удаленный сеанс PowerShell для виртуальной машины поставщика ресурсов и вызвать команду. При необходимости вы можете указать параметры FromDate и ToDate. Если их не указать (или указать только один), в поле ToDate будет указано текущее время, а в поле FromDate — значение времени за четыре часа до текущего.

В следующем примере сценария показано использование этих команд.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>Дополнительная информация
[Добавление серверов размещения SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
