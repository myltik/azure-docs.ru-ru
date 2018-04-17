---
title: Использование баз данных MySQL как PaaS в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть поставщик ресурсов MySQL и предоставить базы данных MySQL как услугу в Azure Stack.
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
ms.openlocfilehash: 66e1d5691b431be0c3d040570b13e8d16b1669ef
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Использование баз данных MySQL в Microsoft Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете развернуть поставщик ресурсов MySQL в Azure Stack. После этого вы можете создать серверы и базы данных MySQL, используя шаблоны развертывания Azure Resource Manager. Можно также предоставить базы данных MySQL как службу. 

Базы данных MySQL, которые обычно используются на веб-сайтах, поддерживают множество сайтовых платформ. Например, после развертывания поставщика ресурсов вы можете создать веб-сайты WordPress с помощью надстройки PaaS веб-приложений для Azure Stack.

Чтобы развернуть поставщик MySQL в системе без доступа к Интернету, скопируйте файл [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) в локальный общий ресурс. Затем введите имя этого общего ресурса при появлении запроса. Необходимо установить модули PowerShell для Azure и Azure Stack.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Архитектура адаптера поставщика ресурсов сервера MySQL Server

Поставщик ресурсов состоит из трех компонентов:

- **Виртуальная машина (Windows) для адаптера поставщика ресурсов MySQL**, на которой работают службы поставщика.

- **Собственно поставщик ресурсов**, который обрабатывает запросы на подготовку ресурсов базы данных и предоставляет к ним доступ.

- **Серверы, на которых размещается сервер MySQL Server**. Эти серверы размещения предоставляют емкость для баз данных.

В этом выпуске больше не создаются экземпляры MySQL. Это означает, что необходимо создать их самостоятельно и (или) предоставить доступ к внешним экземплярам SQL. Посетите [коллекцию быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows), чтобы получить шаблон, который позволяет:
- создать сервер MySQL;
- скачать и развернуть сервер MySQL Server из Microsoft Azure Marketplace.

> [!NOTE]
> Серверы размещения, установленные в интегрированных системах Azure Stack, необходимо создавать на основе подписки клиента. Их нельзя создать из подписки поставщика по умолчанию. Эти серверы необходимо создавать на портале клиента или в сеансе PowerShell, используя соответствующее имя для входа. Все серверы размещения — это оплачиваемые виртуальные машины, у которых должны быть соответствующие лицензии. Администратор служб может быть владельцем подписки клиента.

### <a name="required-privileges"></a>Необходимые привилегии
У системной учетной записи должны быть такие разрешения:

1.  база данных — создание, удаление;
2.  имя для входа — создание, установка параметров, удаление, предоставление разрешений, отмена.

## <a name="deploy-the-resource-provider"></a>Развертывание поставщика ресурсов

1. Если вы не сделали этого ранее, зарегистрируйте пакет SDK и скачайте образ основных компонентов Windows Server 2016 Datacenter, который доступен в разделе "Marketplace Management" (Управление Marketplace). Использовать можно только образ основных компонентов Windows Server 2016. Вы можете применить сценарий, чтобы создать [образ Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Но обязательно выберите образ основных компонентов).


2. Войдите на узел, на котором доступна виртуальная машина привилегированной конечной точки.

    - Если установлен пакет SDK для Azure, войдите на физический узел. 
    - Если в системе несколько узлов, нужно использовать тот, который предоставляет доступ к привилегированной конечной точке.
    
    >[!NOTE]
    > Сценарий *должен* выполняться в системе Windows 10 или Windows Server 2016 с установленной последней версией среды выполнения .NET. В противном случае произойдет сбой установки. Узел с пакетом SDK для Azure Stack соответствуют этому критерию.
    

3. Скачайте двоичный файл адаптера поставщика ресурсов MySQL. Затем запустите самоизвлечение содержимого во временный каталог.

    >[!NOTE] 
    > У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack. Обязательно скачайте соответствующий двоичный файл для выполняемой версии Azure Stack.

    | Сборка Azure Stack | Установщик MySQL RP |
    | --- | --- |
    | 1802: 1.0.180302.1 | [MySQL RP версии 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712: 1.0.180102.3 или 1.0.180106.1 (несколько узлов) | [MySQL RP версии 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1711: 1.0.171122.1 | [MySQL RP версии 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1710: 1.0.171028.1 | [MySQL RP версии 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  Для пакета SDK для Azure в рамках этого процесса создается самозаверяющий сертификат. Для системы с несколькими узлами вам нужно предоставить подходящий сертификат.

    Если нужно предоставить собственный сертификат, укажите в параметре **DependencyFilesLocalPath** PFX-файл, соответствующий следующим критериям.

    - Групповой сертификат для \*.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\> или сертификат для одного сайта с общим именем mysqladapter.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\>

    - Это должен быть доверенный сертификат. То есть должна существовать цепочка доверия без промежуточных сертификатов.

    - В DependencyFilesLocalPath имеется только один файл сертификата.
    
    - Имя файла не должно содержать специальные знаки или пробелы.


5. Откройте **новую** консоль PowerShell с повышенными правами (администратора). Затем перейдите в каталог, в который были извлечены файлы. Откройте новое окно, чтобы избежать проблем с неправильными модулями PowerShell, уже загруженными в систему.

6. [Установите Azure PowerShell версии 1.2.11](azure-stack-powershell-install.md).

7. Выполните скрипт `DeployMySqlProvider.ps1`.

Этот скрипт выполняет следующие действия:

* Скачивает двоичный файл соединителя MySQL (его можно указать на автономном источнике).
* Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack.
* Публикует пакеты коллекции, что позволяет развертывать базы данных SQL из коллекции.
* Публикует пакет коллекции для развертывания серверов размещения.
* Развертывает виртуальную машину из образа Windows Server 2016, который вы создали на шаге 1. Он также устанавливает поставщик ресурсов.
* Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
* Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager (для клиента и администратора).


Вы можете:
- Указать обязательные параметры в командной строке.
- Выполнить сценарий без параметров и ввести их при появлении соответствующего запроса.

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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate
# file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploymysqlproviderps1-parameters"></a>Параметры DeployMySqlProvider.ps1
Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые параметры.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ |
| **VMLocalCredential** | Учетные данные локального администратора на виртуальной машине поставщика ресурсов MySQL. | _Обязательный_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ |
| **DependencyFilesLocalPath** | Путь к локальному общему ресурсу, содержащему файл [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Если вы указали один из этих путей, в этот каталог нужно поместить и файл сертификата. | _Необязательно._ (_Обязательно_, если в системе несколько узлов). |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ |
| **MaxRetryCount** | Количество повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет  |
| **AcceptLicense** | Пропускает запрос на принятие условий лицензии GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Проверка развертывания с использованием портала Azure Stack

> [!NOTE]
>  После выполнения сценария установки необходимо обновить страницу портала, чтобы появилась колонка администратора.


1. Войдите на портал администрирования в качестве администратора служб.

2. Проверьте, успешно ли выполнено развертывание. Последовательно выберите **Группы ресурсов**, затем выберите группу ресурсов **system.\<расположение\>.mysqladapter**. Убедитесь, что все четыре развертывания выполнены успешно.

      ![Проверка развертывания поставщика ресурсов MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Указание емкости путем подключения к серверу размещения MySQL

1. Войдите на портал Azure Stack в качестве администратора служб.

2. Щелкните **Ресурсы администрирования** > **MySQL Hosting Servers (Серверы размещения MySQL)** > **+Добавить**.

    В колонке **MySQL Hosting Servers** (Серверы размещения MySQL) можно подключить поставщик ресурсов сервера MySQL Server к фактическим экземплярам MySQL Server, которые выступают в качестве серверной части поставщика ресурсов.

    ![Серверы размещения](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Введите сведения о подключении своего экземпляра MySQL Server. Обязательно укажите полное доменное имя или допустимый IPv4-адрес, но не короткое имя виртуальной машины. В этой версии больше не содержится экземпляр MySQL по умолчанию. Укажите размер, позволяющий упростить управление емкостью базы данных для поставщика ресурсов. Его значение должно быть близким к физической емкости сервера базы данных.

    > [!NOTE]
    >Если экземпляр MySQL может быть доступен Azure Resource Manager клиента и администратора, он может находиться под контролем поставщика ресурсов. Экземпляр MySQL *должен* быть выделен исключительно для поставщика ресурсов.

4. При добавлении серверов им необходимо присваивать новые или имеющиеся номера SKU, чтобы различать предложения служб.
  Например, у вас может быть корпоративный экземпляр, обеспечивающий:
    - емкость базы данных;
    - автоматическое резервное копирование;
    - резервирование высокопроизводительных серверов для отдельных подразделений.
 

Название SKU должно отражать свойства, чтобы клиенты могли размещать свои базы данных соответствующим образом. У всех серверов размещения под одним номером SKU должны быть одинаковые возможности.

![Создание SKU MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> Отображение номеров SKU на портале может занять до часа. Вы не сможете создать базу данных, пока не будет создан номер SKU.


## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>Чтобы протестировать развертывание, создайте первую базу данных MySQL.


1. Войдите на портал Azure Stack в качестве администратора служб.

2. Щелкните **+ Создать** > **Данные+хранилище** > **База данных MySQL**.

3. Укажите сведения о базе данных.

    ![Создание тестовой базы данных MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Выберите SKU.

    ![Выбор SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Создайте набор параметров для входа. Можно повторно использовать существующее имя для входа или создать новое. В нем содержатся такие параметры, как имя пользователя и пароль базы данных.

    ![Создание набора параметров для входа в базу данных](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    В строке подключения содержится имя сервера реальной базы данных. Скопируйте его на портале.

    ![Получение строки подключения для базы данных MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> Длина имени пользователя в MySQL 5.7 не может превышать 32 знаков. В более ранних версиях она не может превышать 16 знаков.


## <a name="add-capacity"></a>Добавление емкости

Увеличьте емкость, подключив новые серверы MySQL на портале Azure Stack. Дополнительные серверы можно добавить к новому или существующему SKU. Убедитесь, что у серверов одинаковые характеристики.


## <a name="make-mysql-databases-available-to-tenants"></a>Предоставление клиентам доступа к базам данных MySQL
Создавайте планы и предложения, чтобы сделать базы данных MySQL доступными для клиентов. Например, добавьте службу Microsoft.MySqlAdapter, квоту и т. д.

![Создание планов и предложений для включения в них баз данных](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Обновление пароля администратора
Пароль можно изменить, но сначала это нужно сделать в экземпляре сервера MySQL. Щелкните **Ресурсы администрирования** > **MySQL Hosting Servers** (Серверы размещения MySQL). Выберите сервер размещения. На панели **Параметры** щелкните **Пароль**.

![Обновление пароля администратора](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Обновление адаптера поставщика ресурсов MySQL (только с несколькими узлами, сборка 1710 и более поздние версии)
При обновлении сборки Azure Stack может быть выпущен новый адаптер поставщика ресурсов SQL. Имеющийся адаптер может продолжать работу. Тем не менее мы рекомендуем как можно скорее обновить его до последней сборки. 

Чтобы обновить поставщик ресурсов, воспользуйтесь сценарием *UpdateMySQLProvider.ps1*. Этот процесс очень похож на процесс установки поставщика ресурсов, который описан в этой статье в разделе [Развертывание поставщика ресурсов](#deploy-the-resource-provider). Сценарий входит в состав скачиваемых ресурсов поставщика ресурсов.

Сценарий *UpdateMySQLProvider.ps1* создает виртуальную машину с кодом последней версии поставщика ресурсов и переносит параметры из старой виртуальной машины на новую. Переносятся данные о базе данных и сервере размещения, а также необходимая запись DNS.

Этот сценарий необходимо использовать с аргументами, описанными для сценария DeployMySqlProvider.ps1. Также укажите сертификат. 

Ниже приведен пример сценария *UpdateMySQLProvider.ps1*, который можно запустить в командной строке PowerShell. (Не забудьте изменить данные учетной записи и пароли, если это требуется). 

> [!NOTE]
> Этот процесс обновления применим только к интегрированным системам.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>Параметры UpdateMySQLProvider.ps1
Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые параметры.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ |
| **VMLocalCredential** |Учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _Обязательный_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ |
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _Необязательно._ (_Обязательно_, если в системе несколько узлов). |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ |
| **MaxRetryCount** | Количество повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет  |
| **AcceptLicense** | Пропускает запрос на принятие условий лицензии GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="collect-diagnostic-logs"></a>Сбор данных журналов диагностики
Поставщик ресурсов MySQL представляет собой заблокированную виртуальную машину. При необходимости сбора данных журнала с виртуальной машины предоставляется конечная точка PowerShell Just Enough Administration (JEA) _DBAdapterDiagnostics_. С помощью этой конечной точки можно выполнить две команды:

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
Поставщик ресурсов MySQL представляет собой заблокированную виртуальную машину. Обновление безопасности виртуальной машины поставщика ресурсов может быть выполнено через конечную точку PowerShell Just Enough Administration (JEA) _DBAdapterMaintenance_.

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

2. Создайте сеанс PowerShell для конечной точки обслуживания виртуальной машины для адаптера поставщика ресурсов MySQL RP.
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


## <a name="remove-the-mysql-resource-provider-adapter"></a>Удаление адаптера поставщика ресурсов MySQL

Чтобы удалить поставщик ресурсов, сначала нужно удалить все его зависимости.

1. Найдите исходный пакет развертывания, который вы скачали ранее для своей версии поставщика ресурсов.

2. Из поставщика ресурсов следует удалить все базы данных клиента. (При удалении баз данных клиента данные не удаляются). Эту задачу должны выполнить клиенты.

3. Клиентам необходимо отменить регистрацию в пространстве имен.

4. Администратору следует удалить серверы размещения из адаптера MySQL.

5. Администратору необходимо удалить все планы, которые ссылаются на этот адаптер MySQL.

6. Администратору следует удалить все квоты, связанные с этим адаптером MySQL.

7. Повторно запустите сценарий развертывания со следующими элементами:
    - параметр -Uninstall;
    - конечные точки Azure Resource Manager;
    - DirectoryTenantID;
    - учетные данные администратора служб.
