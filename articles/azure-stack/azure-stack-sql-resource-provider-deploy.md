---
title: "Использование баз данных SQL в Azure Stack | Документация Майкрософт"
description: "Узнайте, как развернуть базы данных SQL в качестве службы для Azure Stack, и изучите простую процедуру развертывания адаптера поставщика ресурсов SQL Server."
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: JeffGo
ms.openlocfilehash: bf52ed4986b4e0930b57721c0e38bbf748045a36
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
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

## <a name="deploy-the-resource-provider"></a>Развертывание поставщика ресурсов

1. Если вы не сделали этого ранее, зарегистрируйте пакет SDK и скачайте образ основных компонентов Windows Server 2016 Datacenter, который доступен в разделе Marketplace Management (Управление Marketplace). Использовать можно только образ основных компонентов Windows Server 2016. Вы можете применить сценарий, чтобы создать [образ Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Но обязательно выберите образ основных компонентов). Среда выполнения .NET 3.5 больше не требуется.

2. Войдите на узел, на котором доступна виртуальная машина привилегированной конечной точки.

    - Если используется пакет средств разработки Azure Stack, войдите на физический узел.

    - Если в системе несколько узлов, нужно использовать тот, который предоставляет доступ к привилегированной конечной точке.
    
    >[!NOTE]
    > Сценарий *должен* выполняться в системе Windows 10 или Windows Server 2016 с установленной последней версией среды выполнения .NET. В противном случае произойдет сбой установки. Узел с пакетом SDK для Azure Stack соответствуют этому критерию.


3. Скачайте двоичный файл адаптера поставщика ресурсов SQL. Затем запустите самоизвлечение содержимого во временный каталог.

    >[!NOTE] 
    > Сборка поставщика ресурсов соответствует сборкам Azure Stack. Обязательно скачайте соответствующий двоичный файл для выполняемой версии Azure Stack.

    | Сборка Azure Stack | Установщик поставщика ресурсов SQL |
    | --- | --- |
    |1.0.180102.3, 1.0.180103.2 или 1.0.180106.1 (несколько узлов) | [SQL RP версии 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1.0.171122.1 | [SQL RP версии 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1.0.171028.1 | [SQL RP версии 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Корневой сертификат Azure Stack извлекается из привилегированной конечной точки. Для пакета SDK для Azure Stack в рамках этого процесса создается самозаверяющий сертификат. Для системы с несколькими узлами вам нужно предоставить подходящий сертификат.

   Чтобы предоставить собственный сертификат, укажите PFX-файл в параметре **DependencyFilesLocalPath** следующим образом.

    - Групповой сертификат для \*.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\> или сертификат для одного сайта с общим именем sqladapter.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\>

    - Это должен быть доверенный сертификат. То есть должна существовать цепочка доверия без промежуточных сертификатов.

    - В DependencyFilesLocalPath имеется только один файл сертификата.

    - Имя файла не должно содержать специальные знаки.


5. Откройте **новую** консоль PowerShell с повышенными привилегиями (с правами администратора) и перейдите к каталогу, в который вы ранее извлекли файлы. Откройте новое окно, чтобы избежать проблем с неправильными модулями PowerShell, уже загруженными в систему.

6. [Установите Azure PowerShell версии 1.2.11](azure-stack-powershell-install.md).

7. Запустите сценарий DeploySqlProvider.ps1, который выполняет следующие действия.

    - Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack.
    - Публикует пакеты коллекции, что позволяет развертывать базы данных SQL из коллекции.
    - Публикует пакет коллекции для развертывания серверов размещения.
    - Развертывает виртуальную машину из образа Windows Server 2016, который вы создали на шаге 1, и устанавливает поставщик ресурсов.
    - Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
    - Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager (для пользователя и администратора).

> [!NOTE]
> Если установка занимает более 90 минут, может произойти сбой. В этом случае на экране и в файле журнала отобразится сообщение об ошибке, но развертывание будет возобновлено с проблемного этапа. Если система не соответствует рекомендуемым характеристикам для памяти и виртуальных ЦП, в ней невозможно развернуть поставщик ресурсов SQL.
>

Ниже приведен пример, который можно запустить в командной строке PowerShell. (Не забудьте изменить данные учетной записи и пароли, если это требуется).

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
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
При обновлении сборки Azure Stack выпускается новый адаптер поставщика ресурсов SQL. Существующий адаптер может продолжать работу. Тем не менее мы рекомендуем обновить его до последней сборки как можно быстрее после обновления Azure Stack. 

Процесс обновления очень похож на процесс установки, описанный выше. Создайте новую виртуальную машину с последним кодом поставщика ресурсов. Кроме того, следует перенести параметры на этот новый экземпляр, включая базу данных и данные сервера размещения. Можно также перенести необходимую запись DNS.

Используйте сценарий UpdateSQLProvider.ps1 с аргументами, описанными ранее. Здесь также необходимо указать сертификат.

> [!NOTE]
> Этот процесс обновления поддерживается только в системах с несколькими узлами.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

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
