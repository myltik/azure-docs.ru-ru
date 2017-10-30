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
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 8d6faff118b46d8014078d0f1dcc00d125994e49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Использование баз данных SQL в Microsoft Azure Stack

*Область применения: интегрированные системы Azure Stack и пакет SDK для Azure Stack*

Адаптер поставщика ресурсов SQL Server используется для предоставления базы данных SQL в качестве службы [Azure Stack](azure-stack-poc.md). Когда вы установите поставщик ресурсов и подключите его к одному или нескольким экземплярам SQL Server, вы и ваши пользователи сможете создавать:
- базы данных для приложений, созданных для облака;
- веб-сайты, основанные на SQL;
- рабочие нагрузки, основанные на SQL. Вам не придется каждый раз подготавливать виртуальную машину для размещения SQL Server.

Поставщик ресурсов поддерживает не все возможности [базы данных SQL Azure](https://azure.microsoft.com/services/sql-database/) по управлению базами данных. Например, недоступны пулы эластичных баз данных и возможность автоматически повышать и понижать производительность базы данных. Но поставщик ресурсов поддерживает аналогичные операции создания, чтения, обновления и удаления (CRUD). API-интерфейс несовместим с базой данных SQL.

## <a name="sql-server-resource-provider-adapter-architecture"></a>Архитектура адаптера поставщика ресурсов SQL Server
Поставщик ресурсов состоит из трех компонентов:

- **Виртуальная машина для адаптера поставщика ресурсов SQL**, на которой выполняется служба поставщика.
- **Собственно поставщик ресурсов**, который обрабатывает запросы на подготовку ресурсов базы данных и предоставляет к ним доступ.
- **Серверы, на которых размещается SQL Server**. Эти серверы размещения поддерживают функционирование баз данных.

Необходимо создать один или несколько серверов SQL и/или предоставить доступ к внешним экземплярам SQL Server.

## <a name="deploy-the-resource-provider"></a>Развертывание поставщика ресурсов SQL

1. Если вы не сделали этого ранее, зарегистрируйте пакет SDK и скачайте образ Windows Server 2016 Datacenter Core, который доступен на Marketplace. Использовать можно только образ Windows Server 2016 Core. Вы можете применить скрипт для создания [образа Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image), но обязательно выберите вариант Core. Среда выполнения .NET 3.5 больше не требуется.

2. Войдите на узел, предоставляющий доступ к виртуальной машине привилегированной конечной точки.
    а. Если используется пакет SDK для Azure Stack (ASDK), выполните вход на физический узел.
    b. Если система содержит несколько узлов, нужно использовать тот, который предоставляет доступ к привилегированной конечной точке.

3. [Скачайте двоичный файл поставщика ресурсов SQL](https://aka.ms/azurestacksqlrp) и извлеките его во временный каталог.

4. Корневой сертификат Azure Stack можно получить из привилегированной конечной точки. Для ASDK в рамках этого процесса создается самозаверяющий сертификат. Для системы с несколькими узлами вам нужно предоставить подходящий сертификат.

    Если сертификат вы будете предоставлять самостоятельно, он должен иметь следующий тип:

    Групповой сертификат для \*.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\>. Этот сертификат должен быть доверенным, например выданным центром сертификации. То есть должна существовать цепочка доверия без промежуточных сертификатов. Можно использовать сертификат для одного узла с явно указанным именем виртуальной машины [sqladapter], которое использовалось при установке.


5. Откройте **новую** консоль PowerShell с повышенными правами (с правами администратора) и перейдите к каталогу, в который вы ранее извлекли файлы. Новое окно нужно, чтобы избежать проблем с неверными модулями PowerShell, установленными в системе ранее.

6. [Установка Azure PowerShell версии 1.2.11](azure-stack-powershell-install.md).

7. Запустите сценарий DeploySqlProvider.ps1 с перечисленными ниже параметрами.

Этот скрипт выполняет следующие действия:

- Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack.
- Публикует пакеты коллекции, которые позволяют развертывать базу данных SQL из коллекции.
- Публикует пакет коллекции для развертывания серверов размещения.
- Развертывает виртуальную машину из образа Windows Server 2016, который вы создали на шаге 1, и устанавливает поставщик ресурсов.
- Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
- Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager (для пользователя и администратора).

> [!NOTE]
> Если установка занимает более 90 минут, может произойти сбой. На экране и в файле журнала отобразится сообщение об ошибке, но развертывание будет возобновлено с проблемного этапа. Если система не соответствует рекомендуемым характеристикам по памяти и ядрам, она не сможет развернуть SQL RP.
>

Ниже приведен пример, который можно запустить из командной строки PowerShell (не забудьте указать правильные данные учетной записи и пароль).

```
# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack
$domain = "AzureStack"
# Extract the downloaded file by executing it and pointing to a temp directory
$tempDir = "C:\TEMP\SQLRP"
# The service admin (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"

# Install the AzureRM.Bootstrapper module
Install-Module -Name AzureRm.BootStrapper -Force

# Install and imports the API Version Profile required by Azure Stack into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Create the credentials needed for the deployment - local VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the Service Admin credential
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# and the cloud admin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
$tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -CloudAdminCredential $cloudAdminCreds -PrivilegedEndpoint '10.10.10.10' -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>Параметры DeploySqlProvider.ps1
Эти параметры можно указать в командной строке. Если вы не предоставите нужные параметры или их значения не пройдут проверку, вам будет предложено предоставить необходимые данные.

| Имя параметра | Описание | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _обязательный параметр_ |
| **AzCredential** | Укажите учетные данные для учетной записи администратора служб Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _обязательный параметр_ |
| **VMLocalCredential** | Укажите учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _обязательный параметр_ |
| **PrivilegedEndpoint** | Укажите IP-адрес или DNS-имя привилегированной конечной точки. |  _обязательный параметр_ |
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _необязательно_ (_обязательно_, если в системе несколько узлов) |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _обязательный параметр_ |
| **MaxRetryCount** | Укажите, сколько раз нужно повторять каждую операцию в случае сбоя.| 2 |
| **RetryDuration** | Укажите время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаление поставщика ресурсов и всех связанных с ним ресурсов (см. примечания ниже) | Нет |
| **DebugMode** | Отключение автоматической очистки в случае ошибки | Нет |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Проверка развертывания с помощью портала Azure Stack

> [!NOTE]
>  После выполнения скрипта установки необходимо обновить портал, чтобы появилась колонка администратора.


1. Войдите на портал администратора в качестве администратора служб.

2. Проверьте успешность развертывания. Найдите элемент **Группы ресурсов** &gt;, щелкните группу ресурсов **system.<location>.sqladapter** и убедитесь, что все пять развертываний завершены успешно.

      ![Проверка развертывания SQL RP](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)





## <a name="removing-the-sql-adapter-resource-provider"></a>Удаление адаптера поставщика ресурсов SQL

Чтобы удалить поставщик ресурсов, сначала нужно удалить все зависимости.

1. Найдите исходный пакет развертывания, который вы ранее скачали для вашей версии поставщика ресурсов.

2. Из поставщика ресурсов следует удалить все пользовательские базы данных (сами данные при этом не удаляются). Эти действия должны выполнить пользователи.

3. Администратор должен удалить серверы размещения из адаптера SQL.

4. Администратор должен удалить все планы, которые ссылаются на этот адаптер SQL.

5. Администратор должен удалить все номера SKU и квоты, связанные с этим адаптером SQL.

6. Снова запустите скрипт развертывания с параметром -Uninstall и укажите конечные точки Azure Resource Manager, идентификатор DirectoryTenantID и учетные данные учетной записи администратора служб.


## <a name="next-steps"></a>Дальнейшие действия


Изучите другие [службы PaaS](azure-stack-tools-paas-services.md), такие как [поставщик ресурсов MySQL Server](azure-stack-mysql-resource-provider-deploy.md) или [поставщик ресурсов службы приложений](azure-stack-app-service-overview.md).
