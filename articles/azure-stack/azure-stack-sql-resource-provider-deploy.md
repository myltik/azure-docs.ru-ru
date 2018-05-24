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
ms.openlocfilehash: 20b289c16a73bd20ed020987116975c8abe893f0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198581"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Использование баз данных SQL в Microsoft Azure Stack
Используйте поставщик ресурсов SQL Server, чтобы предоставлять доступ к базам данных SQL посредством службы Azure Stack. Служба поставщика ресурсов SQL запущена на виртуальной машине поставщика ресурсов SQL. Это виртуальная машина Windows Server Core.

## <a name="prerequisites"></a>предварительным требованиям
Существует несколько предварительных требований, которые должны быть выполнены перед развертыванием поставщика ресурсов SQL Azure Stack. На компьютере, у которого есть доступ к виртуальной машине привилегированной конечной точки, выполните следующие действия:

- [Зарегистрируйте Azure Stack в Azure](.\azure-stack-registration.md), чтобы можно было скачивать элементы Azure Marketplace, если это еще не было сделано.
- Добавьте необходимую виртуальную машину Windows Server Core в Azure Stack Marketplace, скачав образ **Windows Server 2016 Server Core**. Если необходимо установить обновление, один MSU-пакет можно поместить в локальный каталог, путь к которому задан в системе. Если обнаружено несколько MSU-файлов, при установке поставщика ресурсов SQL произойдет сбой.
- Скачайте двоичный файл поставщика ресурсов SQL и запустите файл для самостоятельного извлечения содержимого во временный каталог. У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack. Обязательно скачайте соответствующий двоичный файл для используемой версии Azure Stack:
    - Azure Stack версии 1802 (1.0.180302.1): [Поставщик ресурсов SQL версии 1.1.18.0](https://aka.ms/azurestacksqlrp1802).
    - Azure Stack версии 1712 (1.0.180102.3, 1.0.180103.2 или 1.0.180106.1 (для интегрированных систем)): [Поставщик ресурсов SQL версии 1.1.14.0](https://aka.ms/azurestacksqlrp1712).
- Для установки в интегрированные системы необходимо предоставить сертификат PKI SQL PaaS, как описано в подразделе "Необязательные сертификаты PaaS" раздела [Требования к сертификатам PKI для развертывания Azure Stack](.\azure-stack-pki-certs.md#optional-paas-certificates). Для этого необходимо поместить PFX-файл в расположение, указанное в параметре **DependencyFilesLocalPath**.
- Убедитесь, что у вас установлена [последняя версия Azure Stack PowerShell](.\azure-stack-powershell-install.md) (1.2.11). 

## <a name="deploy-the-sql-resource-provider"></a>Развертывание поставщика ресурсов SQL
Выполнив все предварительные требования для установки поставщика ресурсов SQL, вы можете запустить сценарий **DeploySqlProvider.ps1**, чтобы развернуть поставщик ресурсов SQL. Сценарий DeploySqlProvider.ps1 входит в состав двоичного файла поставщика ресурсов SQL, скачанного в соответствии с вашей версией Azure Stack. 

> [!IMPORTANT]
> Сценарий должен выполняться в системе Windows 10 или Windows Server 2016, в которой установлена последняя версия среды выполнения .NET.


Чтобы развернуть поставщик ресурсов SQL, откройте новую консоль PowerShell с повышенными привилегиями (с правами администратора) и перейдите в каталог, в который ранее извлекли двоичные файлы поставщика ресурсов SQL.

> [!NOTE]
> Откройте новое окно консоли PowerShell, чтобы избежать проблем с неправильными модулями PowerShell, уже загруженными в систему.

Запустите сценарий DeploySqlProvider.ps1, который выполняет следующие действия:
- Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack.
- Публикует пакеты коллекции, что позволяет развертывать базы данных SQL из коллекции.
- Публикует пакет коллекции для развертывания серверов размещения.
- Развертывает виртуальную машину из образа Windows Server 2016, который вы создали на шаге 1, и устанавливает поставщик ресурсов.
- Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
- Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager (для пользователя и администратора).
- При необходимости устанавливает одно обновление Windows во время установки поставщика ресурсов.

Развертывание поставщика ресурсов SQL будет запущено, и будет создана группа ресурсов system.local.sqladapter. На выполнение четырех обязательных развертываний в эту группу ресурсов может потребоваться до 75 минут.

### <a name="deploysqlproviderps1-parameters"></a>Параметры DeploySqlProvider.ps1
Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые параметры.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ |
| **VMLocalCredential** | Учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _Обязательный_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ |
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _Необязательный_ (_обязательно_ для интегрированных систем) |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ |
| **MaxRetryCount** | Количество повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет  |

>[!NOTE]
> Отображение номеров SKU на портале может занять до часа. Вы не сможете создать базу данных, пока не будет создан номер SKU.


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Развертывание поставщика ресурсов SQL с помощью пользовательского сценария
Чтобы избежать ручного ввода необходимых сведений при запуске сценария DeploySqlProvider.ps1, можно изменить следующий пример сценария, указав сведения об учетной записи по умолчанию и паролях:

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

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Проверка развертывания с использованием портала Azure Stack
С помощью действий, описанных в этом разделе, можно убедиться, что поставщик ресурсов SQL был развернут успешно.

> [!NOTE]
>  Выполнив сценарий установки, необходимо обновить страницу портала, чтобы появились колонка администратора и элементы коллекции.

1. Войдите на портал администрирования в качестве администратора служб.

2. Проверьте, успешно ли выполнено развертывание. Перейдите на страницу **Группы ресурсов**. Выберите группу ресурсов **system.\<расположение\>.sqladapter**. Убедитесь, что все четыре развертывания выполнены успешно.

      ![Проверка развертывания поставщика ресурсов SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>Дополнительная информация

[Добавление серверов размещения](azure-stack-sql-resource-provider-hosting-servers.md)
