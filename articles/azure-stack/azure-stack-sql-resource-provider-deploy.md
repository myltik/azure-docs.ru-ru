---
title: Использование баз данных SQL в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть базы данных SQL в качестве службы для Azure Stack, и изучите простую процедуру развертывания адаптера поставщика ресурсов SQL Server.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: bd3618367f91fe043cc8412481b38a9c996a5275
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Использование баз данных SQL в Microsoft Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Адаптер поставщика ресурсов SQL Server используется для предоставления базы данных SQL в качестве службы [Azure Stack](azure-stack-poc.md). Когда вы установите поставщик ресурсов и подключите его к одному или нескольким экземплярам SQL Server, вы и ваши пользователи сможете создавать:
- базы данных для приложений, созданных для облака;
- веб-сайты на основе SQL;
- рабочие нагрузки на основе SQL.
Вам не придется каждый раз подготавливать виртуальную машину для размещения SQL Server.

Поставщик ресурсов поддерживает не все возможности [базы данных SQL Azure](https://azure.microsoft.com/services/sql-database/) по управлению базами данных. Например, недоступны пулы эластичных баз данных и возможность автоматически повышать и понижать производительность базы данных. Но поставщик ресурсов поддерживает аналогичные операции создания, чтения, обновления и удаления (CRUD). API несовместим со службой "База данных SQL".

## <a name="sql-resource-provider-adapter-architecture"></a>Архитектура адаптера поставщика ресурсов SQL
Поставщик ресурсов состоит из трех компонентов:

- **Виртуальная машина для адаптера поставщика ресурсов SQL**, на которой выполняется служба поставщика.
- **Собственно поставщик ресурсов**, который обрабатывает запросы на подготовку ресурсов базы данных и предоставляет к ним доступ.
- **Серверы, на которых размещается SQL Server**. Эти серверы размещения поддерживают функционирование баз данных.

Необходимо создать один или несколько экземпляров SQL Server и (или) предоставить доступ к внешним экземплярам SQL Server.

> [!NOTE]
> Серверы размещения, установленные в интегрированных системах Azure Stack, необходимо создавать на основе подписки клиента. Их нельзя создать из подписки поставщика по умолчанию. Эти серверы необходимо создавать на портале клиента или в сеансе PowerShell, используя соответствующее имя для входа. Все серверы размещения — это оплачиваемые виртуальные машины, у которых должны быть соответствующие лицензии. Администратор служб может быть владельцем подписки клиента.

## <a name="deploy-the-resource-provider"></a>Развертывание поставщика ресурсов

1. Если вы не сделали этого ранее, зарегистрируйте пакет SDK и скачайте образ основных компонентов Windows Server 2016 Datacenter, который доступен в разделе Marketplace Management (Управление Marketplace). Использовать можно только образ основных компонентов Windows Server 2016. Вы можете применить сценарий, чтобы создать [образ Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Но обязательно выберите образ основных компонентов).

2. Войдите на узел, на котором доступна виртуальная машина привилегированной конечной точки.

    - Если используется пакет средств разработки Azure Stack, войдите на физический узел.

    - Если в системе несколько узлов, нужно использовать тот, который предоставляет доступ к привилегированной конечной точке.
    
    >[!NOTE]
    > Сценарий *должен* выполняться в системе Windows 10 или Windows Server 2016 с установленной последней версией среды выполнения .NET. В противном случае произойдет сбой установки. Узел с пакетом SDK для Azure Stack соответствуют этому критерию.


3. Скачайте двоичный файл адаптера поставщика ресурсов SQL. Затем запустите самоизвлечение содержимого во временный каталог.

    >[!NOTE] 
    > У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack. Обязательно скачайте соответствующий двоичный файл для выполняемой версии Azure Stack.

    | Сборка Azure Stack | Установщик поставщика ресурсов SQL |
    | --- | --- |
    | 1802: 1.0.180302.1 | [SQL RP версии 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3, 1.0.180103.2 или 1.0.180106.1 (несколько узлов) | [SQL RP версии 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [SQL RP версии 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [SQL RP версии 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Для пакета SDK для Azure Stack в рамках этого процесса создается самозаверяющий сертификат. Для интегрированных систем вам нужно предоставить подходящий сертификат.

   Чтобы предоставить собственный сертификат, укажите PFX-файл в параметре **DependencyFilesLocalPath** следующим образом.

    - Групповой сертификат для \*.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\> или сертификат для одного сайта с общим именем sqladapter.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\>

    - Это должен быть доверенный сертификат. То есть должна существовать цепочка доверия без промежуточных сертификатов.

    - В каталоге, на который указывает параметр DependencyFilesLocalPath, может содержаться только один файл сертификата.

    - Имя файла не должно содержать специальные знаки или пробелы.


5. Откройте **новую** консоль PowerShell с повышенными привилегиями (с правами администратора) и перейдите к каталогу, в который вы ранее извлекли файлы. Откройте новое окно, чтобы избежать проблем с неправильными модулями PowerShell, уже загруженными в систему.

6. [Установите Azure PowerShell версии 1.2.11](azure-stack-powershell-install.md).

7. Запустите сценарий DeploySqlProvider.ps1, который выполняет следующие действия.

    - Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack.
    - Публикует пакеты коллекции, что позволяет развертывать базы данных SQL из коллекции.
    - Публикует пакет коллекции для развертывания серверов размещения.
    - Развертывает виртуальную машину из образа Windows Server 2016, который вы создали на шаге 1, и устанавливает поставщик ресурсов.
    - Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
    - Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager (для пользователя и администратора).
    - При необходимости устанавливает одно обновление Windows во время установки поставщика ресурсов.

8. Мы рекомендуем загрузить последний образ основных компонентов Windows Server 2016 из служб управления Marketplace. Если необходимо установить обновление, один MSU-пакет можно поместить в локальный каталог, путь к которому задан в системе. Если найдено несколько MSU-файлов, выполнение сценария завершится ошибкой.


Ниже приведен пример, который можно запустить в командной строке PowerShell. (Не забудьте изменить данные учетной записи и пароли, если это требуется).

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>Параметры DeploySqlProvider.ps1
Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые параметры.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ |
| **VMLocalCredential** | Учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _Обязательный_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ |
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _Необязательно._ (_Обязательно_, если в системе несколько узлов). |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ |
| **MaxRetryCount** | Количество повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет  |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Проверка развертывания с использованием портала Azure Stack

> [!NOTE]
>  После выполнения сценария установки необходимо обновить страницу портала, чтобы появилась колонка администратора.


1. Войдите на портал администрирования в качестве администратора служб.

2. Проверьте, успешно ли выполнено развертывание. Перейдите на страницу **Группы ресурсов**. Выберите группу ресурсов **system.\<расположение\>.sqladapter**. Убедитесь, что все четыре развертывания выполнены успешно.

      ![Проверка развертывания поставщика ресурсов SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Обновление адаптера поставщика ресурсов SQL (только с несколькими узлами, сборка 1710 и более поздние версии)
При обновлении сборки Azure Stack может быть выпущен новый адаптер поставщика ресурсов SQL. Имеющийся адаптер может продолжать работу. Тем не менее мы рекомендуем как можно скорее обновить его до последней сборки. Обновление необходимо устанавливать в определенном порядке: версии пропускать нельзя (см. таблицу в шаге 3 в разделе [Развертывание поставщика ресурсов](#deploy-the-resource-provider)).

Чтобы обновить поставщик ресурсов, воспользуйтесь сценарием *UpdateSQLProvider.ps1*. Этот процесс очень похож на процесс установки поставщика ресурсов, который описан в этой статье в разделе [Развертывание поставщика ресурсов](#deploy-the-resource-provider). Сценарий входит в состав скачиваемых ресурсов поставщика ресурсов.

Сценарий *UpdateSQLProvider.ps1* создает новую виртуальную машину с кодом последней версии поставщика ресурсов и переносит параметры из старой виртуальной машины на новую. Переносятся данные о базе данных и сервере размещения, а также необходимая запись DNS.

Этот сценарий необходимо использовать с аргументами, описанными для сценария DeploySqlProvider.ps1. Также укажите сертификат. 

Мы рекомендуем загрузить последний образ основных компонентов Windows Server 2016 из служб управления Marketplace. Если необходимо установить обновление, один MSU-пакет можно поместить в локальный каталог, путь к которому задан в системе. Если найдено несколько MSU-файлов, выполнение сценария завершится ошибкой.

Ниже приведен пример сценария *UpdateSQLProvider.ps1*, который можно запустить в командной строке PowerShell. (Не забудьте изменить данные учетной записи и пароли, если это требуется). 

> [!NOTE]
> Этот процесс обновления применим только к интегрированным системам.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>Параметры UpdateSQLProvider.ps1
Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые параметры.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ |
| **VMLocalCredential** | Учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _Обязательный_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ |
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _Необязательно._ (_Обязательно_, если в системе несколько узлов). |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _обязательный параметр_ |
| **MaxRetryCount** | Количество повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** |Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет  |


## <a name="collect-diagnostic-logs"></a>Сбор данных журналов диагностики
Поставщик ресурсов SQL представляет собой заблокированную виртуальную машину. При необходимости сбора данных журнала с виртуальной машины предоставляется конечная точка PowerShell Just Enough Administration (JEA) _DBAdapterDiagnostics_. С помощью этой конечной точки можно выполнить две команды:

* Get-AzsDBAdapterLog: готовит ZIP-пакет, содержащий журналы диагностики RP, и помещает его в пользовательский диск для сеанса. Эту команду можно вызвать без параметров. Она будет собирать данные журнала за последние четыре часа.
* Remove-AzsDBAdapterLog: очищает имеющиеся пакеты журналов на виртуальной машине поставщика ресурсов.

Во время развертывания поставщика ресурсов или обновления подключения к конечной точке диагностики для извлечения журналов поставщика ресурсов создается учетная запись пользователя с именем _dbadapterdiag_. Пароль этой учетной записи совпадает с паролем, предоставленным для учетной записи локального администратора во время развертывания или обновления.

Чтобы использовать эти команды, необходимо создать удаленный сеанс PowerShell для виртуальной машины поставщика ресурсов и вызвать команду. При необходимости вы можете указать параметры FromDate и ToDate. Если их не указать (или указать только один), в поле ToDate будет указано текущее время, а в поле FromDate — значение времени за четыре часа до текущего.

В следующем примере сценария показано использование этих команд.

```
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

## <a name="maintenance-operations-integrated-systems"></a>Операции обслуживания (интегрированные системы)
Поставщик ресурсов SQL представляет собой заблокированную виртуальную машину. Обновление безопасности виртуальной машины поставщика ресурсов может быть выполнено через конечную точку PowerShell Just Enough Administration (JEA) _DBAdapterMaintenance_.

Для упрощения выполнения этих операций сценарий предоставляется вместе с пакетом установки поставщика ресурсов.

### <a name="update-the-virtual-machine-operating-system"></a>Обновление ОС виртуальной машины
Существует несколько способов обновить виртуальную машину с Windows Server:
* Установка пакета последней версии поставщика ресурсов с помощью текущего исправленного образа основных компонентов Windows Server 2016.
* Установка пакета обновления Windows во время установки или обновления поставщика ресурсов.


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>Обновление определений Защитника Windows на виртуальной машине

Чтобы обновить определения Защитника, выполните следующие действия.

1. Загрузите обновление определений Защитника Windows на странице [Definition updates for Windows Defender Antivirus and other Microsoft antimalware](https://www.microsoft.com/en-us/wdsi/definitions) (Обновления определений для антивирусной программы "Защитник Windows" и других антивирусных программ корпорации Майкрософт).

    На этой странице из раздела "Загрузка и установка определений вручную" загрузите 64-битный файл антивирусной программы "Защитник Windows" для Windows 10 и Windows 8.1. 
    
    Прямая ссылка: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Создайте сеанс PowerShell для конечной точки обслуживания виртуальной машины для адаптера поставщика ресурсов SQL.
3. Скопируйте файл обновления определений на компьютер адаптера базы данных, используя сеанс обслуживания конечной точки.
4. Во время сеанса обслуживания PowerShell вызовите команду _Update-DBAdapterWindowsDefenderDefinitions_.
5. После установки рекомендуем удалить использованный файл обновления определений. Его можно удалить во время сеанса обслуживания с помощью команды _Remove-ItemOnUserDrive)_.


Ниже приведен пример сценария для обновления определений Защитника (замените адрес или имя виртуальной машины на фактическое значение).

```
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

## <a name="remove-the-sql-resource-provider-adapter"></a>Удаление адаптера поставщика ресурсов SQL

Чтобы удалить поставщик ресурсов, сначала нужно удалить все его зависимости.

1. Найдите исходный пакет развертывания, который вы скачали ранее для этой версии адаптера поставщика ресурсов SQL.

2. Из поставщика ресурсов следует удалить все пользовательские базы данных. (При удалении пользовательских баз данных данные не удаляются). Эту задачу должны выполнить пользователи.

3. Администратору необходимо удалить серверы размещения из адаптера поставщика ресурсов SQL.

4. Администратору необходимо удалить все планы, которые ссылаются на этот адаптер поставщика ресурсов SQL.

5. Администратору необходимо удалить все номера SKU и квоты, связанные с этим адаптером поставщика ресурсов SQL.

6. Повторно запустите сценарий развертывания со следующими элементами:
    - параметр -Uninstall;
    - конечные точки Azure Resource Manager;
    - DirectoryTenantID;
    - учетные данные администратора служб.


## <a name="next-steps"></a>Дополнительная информация

[Добавьте серверы размещения](azure-stack-sql-resource-provider-hosting-servers.md) и [создайте базы данных](azure-stack-sql-resource-provider-databases.md).

Изучите другие [службы PaaS](azure-stack-tools-paas-services.md), такие как [поставщик ресурсов MySQL Server](azure-stack-mysql-resource-provider-deploy.md) или [поставщик ресурсов службы приложений](azure-stack-app-service-overview.md).
