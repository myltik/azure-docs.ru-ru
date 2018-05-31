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
ms.openlocfilehash: 9154f509f9019c28515970869678aa6633d16163
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206175"
---
# <a name="update-the-sql-resource-provider-adapter"></a>Обновление адаптера поставщика ресурсов SQL
При обновлении сборки Azure Stack может быть выпущен новый адаптер поставщика ресурсов SQL. Имеющийся адаптер может продолжать работу. Тем не менее мы рекомендуем как можно скорее обновить его до последней сборки. Обновления необходимо устанавливать в определенном порядке: версии пропускать нельзя (изучите список версий в разделе [Предварительные требования](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)).

Чтобы обновить поставщик ресурсов, воспользуйтесь сценарием *UpdateSQLProvider.ps1*. Этот процесс очень похож на процесс установки поставщика ресурсов, который описан в разделе [Развертывание поставщика ресурсов SQL](.\azure-stack-sql-resource-provider-deploy.md). Сценарий входит в состав скачиваемых ресурсов поставщика ресурсов.

Сценарий *UpdateSQLProvider.ps1* создает новую виртуальную машину с кодом последней версии поставщика ресурсов и переносит параметры из старой виртуальной машины на новую. Переносятся данные о базе данных и сервере размещения, а также необходимая запись DNS.

Этот сценарий необходимо использовать с аргументами, описанными для сценария DeploySqlProvider.ps1. Также укажите сертификат. 

Мы рекомендуем загрузить последний образ основных компонентов Windows Server 2016 из служб управления Marketplace. Если необходимо установить обновление, один MSU-пакет можно поместить в локальный каталог, путь к которому задан в системе. Если найдено несколько MSU-файлов, выполнение сценария завершится ошибкой.

Ниже приведен пример сценария *UpdateSQLProvider.ps1*, который можно запустить в командной строке PowerShell. (Не забудьте изменить данные учетной записи и пароли, если это требуется). 

> [!NOTE]
> Этот процесс обновления применим только к интегрированным системам.

```powershell
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

## <a name="updatesqlproviderps1-parameters"></a>Параметры UpdateSQLProvider.ps1
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


## <a name="next-steps"></a>Дополнительная информация

[Обслуживание поставщика ресурсов SQL](azure-stack-sql-resource-provider-maintain.md)
