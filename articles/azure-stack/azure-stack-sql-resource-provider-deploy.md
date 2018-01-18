---
title: "Использование баз данных SQL в Azure Stack | Документация Майкрософт"
description: "Узнайте, как развернуть базы данных SQL в качестве службы для Azure Stack, и изучите простую процедуру развертывания адаптера поставщика ресурсов SQL Server"
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
ms.date: 12/15/2017
ms.author: JeffGo
ms.openlocfilehash: 4d6683a1a80dfdccdc5d46e9bac095a0d9f4d3e1
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Использование баз данных SQL в Microsoft Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

Адаптер поставщика ресурсов SQL Server используется для предоставления базы данных SQL в качестве службы [Azure Stack](azure-stack-poc.md). Когда вы установите поставщик ресурсов и подключите его к одному или нескольким экземплярам SQL Server, вы и ваши пользователи сможете создавать:
- базы данных для приложений, созданных для облака;
- веб-сайты, основанные на SQL;
- рабочие нагрузки, основанные на SQL. Вам не придется каждый раз подготавливать виртуальную машину для размещения SQL Server.

Поставщик ресурсов поддерживает не все возможности [базы данных SQL Azure](https://azure.microsoft.com/services/sql-database/) по управлению базами данных. Например, недоступны пулы эластичных баз данных и возможность автоматически повышать и понижать производительность базы данных. Но поставщик ресурсов поддерживает аналогичные операции создания, чтения, обновления и удаления (CRUD). API-интерфейс несовместим с базой данных SQL.

## <a name="sql-resource-provider-adapter-architecture"></a>Архитектура адаптера поставщика ресурсов SQL
Поставщик ресурсов состоит из трех компонентов:

- **Виртуальная машина для адаптера поставщика ресурсов SQL**, на которой выполняется служба поставщика.
- **Собственно поставщик ресурсов**, который обрабатывает запросы на подготовку ресурсов базы данных и предоставляет к ним доступ.
- **Серверы, на которых размещается SQL Server**. Эти серверы размещения поддерживают функционирование баз данных.

Необходимо создать один или несколько серверов SQL и/или предоставить доступ к внешним экземплярам SQL Server.

## <a name="deploy-the-resource-provider"></a>Развертывание поставщика ресурсов SQL

1. Если вы не сделали этого ранее, зарегистрируйте пакет SDK и скачайте образ основных компонентов Windows Server 2016 Datacenter, который доступен в разделе Marketplace Management (Управление Marketplace). Использовать можно только образ основных компонентов Windows Server 2016. Вы можете применить сценарий, чтобы создать [образ Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image), но обязательно выберите вариант основных компонентов. Среда выполнения .NET 3.5 больше не требуется.

2. Войдите на узел, на котором доступна виртуальная машина привилегированной конечной точки.

    a. Если используется комплект разработки для Azure Stack (ASDK), войдите на физический узел.

    Б. Если в системе несколько узлов, нужно использовать тот, который предоставляет доступ к привилегированной конечной точке. 
    
    >[!NOTE]
    > Сценарий *должен* выполняться в системе Windows 10 или Windows Server 2016 с установленной последней версией среды выполнения .NET. В противном случае произойдет сбой установки. Узел ASDK соответствует этому критерию.


3. Скачайте двоичный файл поставщика ресурсов SQL и запустите самоизвлечение содержимого во временный каталог.

    >[!NOTE] 
    > Сборке поставщика ресурсов соответствует сборкам Azure Stack. Необходимо скачать надлежащий двоичный файл для выполняемой версии Azure Stack.

    | Сборка Azure Stack | Установщик SQL RP |
    | --- | --- |
    | 1.0.180102.3 | **Подождите, пока не появится дополнительная информация. После обновления Azure Stack вы не сможете установить текущие сборки, но они будут продолжать работать на нескольких узлах.** |
    | 1.0.171122.1 | [SQL RP версии 1.1.12.0](https://aka.ms/azurestacksqlrp) |
    | 1.0.171028.1 | [SQL RP версии 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
    | 1.0.170928.3 | [SQL RP версии 1.1.3.0](https://aka.ms/azurestacksqlrp1709) |
   

4. Корневой сертификат Azure Stack можно получить из привилегированной конечной точки. Для ASDK в рамках этого процесса создается самозаверяющий сертификат. Для системы с несколькими узлами вам нужно предоставить подходящий сертификат.

    Если нужно предоставить собственный сертификат, необходимо разместить PFX-файл в параметре **DependencyFilesLocalPath** (см. ниже) следующим образом:

    - Групповой сертификат для \*.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\> или сертификат для одного сайта с общим именем sqladapter.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\>
    - Этот сертификат должен быть доверенным, например выданным центром сертификации. То есть должна существовать цепочка доверия без промежуточных сертификатов.
    - В DependencyFilesLocalPath имеется только один файл сертификата.
    - Имя файла не должно содержать специальные знаки.


5. Откройте **новую** консоль PowerShell с повышенными привилегиями (с правами администратора) и перейдите к каталогу, в который вы ранее извлекли файлы. Откройте новое окно, чтобы избежать проблем с неверными модулями PowerShell, уже установленными в системе.

6. [Установка Azure PowerShell версии 1.2.11](azure-stack-powershell-install.md).

7. Запустите сценарий DeploySqlProvider.ps1 с перечисленными ниже параметрами.

Этот скрипт выполняет следующие действия:

- Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack.
- Публикует пакеты коллекции, что позволяет развертывать базу данных SQL из коллекции.
- Публикует пакет коллекции для развертывания серверов размещения.
- Развертывает виртуальную машину из образа Windows Server 2016, который вы создали на шаге 1, и устанавливает поставщик ресурсов.
- Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
- Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager (для пользователя и администратора).

> [!NOTE]
> Если установка занимает более 90 минут, может произойти сбой. На экране и в файле журнала отобразится сообщение об ошибке, но развертывание будет возобновлено с проблемного этапа. Если система не соответствует рекомендуемым характеристикам для памяти и виртуальных ЦП, в ней невозможно развернуть поставщик ресурсов SQL.
>

Ниже приведен пример, который можно запустить из командной строки PowerShell (не забудьте указать правильные данные учетной записи и пароль).

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>Параметры DeploySqlProvider.ps1
Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые данные.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _обязательный параметр_ |
| **AzCredential** | Укажите учетные данные для записи администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _обязательный параметр_ |
| **VMLocalCredential** | Укажите учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _обязательный параметр_ |
| **PrivilegedEndpoint** | Укажите IP-адрес или DNS-имя привилегированной конечной точки. |  _обязательный параметр_ |
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _необязательно_ (_обязательно_, если в системе несколько узлов) |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _обязательный параметр_ |
| **MaxRetryCount** | Укажите, сколько раз нужно повторять каждую операцию в случае сбоя.| 2 |
| **RetryDuration** | Укажите время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаление поставщика ресурсов и всех связанных с ним ресурсов (см. примечания ниже) | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае ошибки. | Нет  |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Проверка развертывания с использованием портала Azure Stack

> [!NOTE]
>  После выполнения скрипта установки необходимо обновить портал, чтобы появилась колонка администратора.


1. Войдите на портал администрирования в качестве администратора служб.

2. Проверьте, успешно ли выполнено развертывание. Найдите **Группы ресурсов**&gt;, щелкните группу **system.\<location\>.sqladapter** и убедитесь, что все четыре развертывания завершены успешно.

      ![Проверка развертывания SQL RP](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Обновление адаптера поставщика ресурсов SQL (только с несколькими узлами, сборка 1710 и более поздние версии)
При обновлении сборки Azure Stack будет выпущен новый адаптер поставщика ресурсов SQL. Хотя имеющийся адаптер может продолжать работать, рекомендуется как можно быстрее обновить его до последней сборки после обновления Azure Stack. Процесс обновления очень похож на процесс установки, описанный выше. Будет создана виртуальная машина с последним RP-кодом. Параметры будут перенесены в этот новый экземпляр, включая базу данных и сведения о размещении сервера, а также необходимую запись DNS.

Используйте сценарий UpdateSQLProvider.ps1 с аргументами, описанными выше. Здесь также необходимо указать сертификат.

> [!NOTE]
> Обновление поддерживается только в системах с несколькими узлами.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>Параметры UpdateSQLProvider.ps1
Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые данные.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _обязательный параметр_ |
| **AzCredential** | Укажите учетные данные для записи администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _обязательный параметр_ |
| **VMLocalCredential** | Укажите учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _обязательный параметр_ |
| **PrivilegedEndpoint** | Укажите IP-адрес или DNS-имя привилегированной конечной точки. |  _обязательный параметр_ |
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _необязательно_ (_обязательно_, если в системе несколько узлов) |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _обязательный параметр_ |
| **MaxRetryCount** | Укажите, сколько раз нужно повторять каждую операцию в случае сбоя.| 2 |
| **RetryDuration** | Укажите время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаление поставщика ресурсов и всех связанных с ним ресурсов (см. примечания ниже) | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае ошибки. | Нет  |



## <a name="remove-the-sql-resource-provider-adapter"></a>Удаление адаптера поставщика ресурсов SQL

Чтобы удалить поставщик ресурсов, сначала нужно удалить все зависимости.

1. Найдите исходный пакет развертывания, который вы скачали ранее для этой версии адаптера поставщика ресурсов SQL.

2. Из поставщика ресурсов следует удалить все пользовательские базы данных (сами данные при этом не удаляются). Эти действия должны выполнить пользователи.

3. Администратору необходимо удалить серверы размещения из адаптера поставщика ресурсов SQL.

4. Администратору необходимо удалить все планы, которые ссылаются на этот адаптер поставщик ресурсов SQL.

5. Администратору необходимо удалить все номера SKU и квоты, связанные с этим адаптером поставщика ресурсов SQL.

6. Снова запустите скрипт развертывания с параметром -Uninstall и укажите конечные точки Azure Resource Manager, DirectoryTenantID и учетные данные учетной записи администратора служб.


## <a name="next-steps"></a>Дополнительная информация

[Добавьте серверы размещения](azure-stack-sql-resource-provider-hosting-servers.md) и [создайте базы данных](azure-stack-sql-resource-provider-databases.md).

Изучите другие [службы PaaS](azure-stack-tools-paas-services.md), такие как [поставщик ресурсов MySQL Server](azure-stack-mysql-resource-provider-deploy.md) или [поставщик ресурсов службы приложений](azure-stack-app-service-overview.md).
