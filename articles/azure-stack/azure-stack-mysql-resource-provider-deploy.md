---
title: "Использование баз данных MySQL как PaaS в Azure Stack | Документация Майкрософт"
description: "Узнайте, как развернуть поставщик ресурсов MySQL и предоставить базы данных MySQL как услугу в Azure Stack"
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
ms.date: 01/10/2018
ms.author: JeffGo
ms.openlocfilehash: d0394fd1edf21cdbb863a88a1d3ecef118a7d886
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Использование баз данных MySQL в Microsoft Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

Вы можете развернуть поставщик ресурсов MySQL в Azure Stack. После этого вы можете создать серверы и базы данных MySQL, используя шаблоны развертывания Azure Resource Manager, а затем предоставить эти базы данных как услуги. Базы данных MySQL, которые обычно используются на веб-сайтах, поддерживают множество сайтовых платформ. В качестве примера после развертывания поставщика ресурсов вы можете создать веб-сайты WordPress с помощью надстройки PaaS веб-приложений Azure для Azure Stack.

Чтобы развернуть поставщик MySQL в системе без доступа к Интернету, скопируйте файл [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) в локальный общий ресурс. Затем введите имя этого общего ресурса при появлении запроса. Необходимо также установить модули PowerShell для Azure и Azure Stack.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>Архитектура адаптера поставщика ресурсов MySQL Server

Поставщик ресурсов состоит из трех компонентов:

- **Виртуальная машина (Windows) для адаптера поставщика ресурсов MySQL**, на которой работают службы поставщика.
- **Собственно поставщик ресурсов**, который обрабатывает запросы на подготовку ресурсов базы данных и предоставляет к ним доступ.
- **Серверы, на которых размещается MySQL Server**. Эти серверы размещения предоставляют емкость для баз данных.

В этом выпуске больше не создается экземпляр MySQL. Вам необходимо создать такие экземпляры и/или предоставить доступ к внешним экземплярам SQL. Посетите [коллекцию быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows), чтобы получить шаблон, который позволяет:
- создать сервер MySQL;
- скачать и развернуть MySQL Server из Microsoft Azure Marketplace.

> [!NOTE]
> Любые серверы размещения, установленные в многоузловой инфраструктуре Azure Stack, необходимо создавать на основе подписки клиента. Их нельзя создать из подписки поставщика по умолчанию. Другими словами, серверы необходимо создавать на портале клиента или во время сеанса PowerShell, используя соответствующее имя для входа. Все серверы размещения — это оплачиваемые виртуальные машины, у которых должны быть соответствующие лицензии. Администратор служб может быть владельцем такой подписки.

### <a name="required-privileges"></a>Необходимые привилегии
У системной учетной записи должны быть такие разрешения:

1.  база данных — создание, удаление;
2.  имя для входа — создание, установка параметров, удаление, предоставление разрешений, отмена.

## <a name="deploy-the-resource-provider"></a>Развертывание поставщика ресурсов

1. Если вы не сделали этого ранее, зарегистрируйте пакет SDK и скачайте образ основных компонентов Windows Server 2016 Datacenter, который доступен в разделе Marketplace Management (Управление Marketplace). Использовать можно только образ основных компонентов Windows Server 2016. Вы можете применить сценарий, чтобы создать [образ Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image), но обязательно выберите вариант основных компонентов. Среда выполнения .NET 3.5 больше не требуется.


2. Войдите на узел, на котором доступна виртуальная машина привилегированной конечной точки.

    a. Если используется комплект разработки для Azure Stack (ASDK), войдите на физический узел.

    Б. Если в системе несколько узлов, нужно использовать тот, который предоставляет доступ к привилегированной конечной точке.
    
    >[!NOTE]
    > Сценарий *должен* выполняться в системе Windows 10 или Windows Server 2016 с установленной последней версией среды выполнения .NET. В противном случае произойдет сбой установки. Узел ASDK соответствует этому критерию.
    

3. Скачайте двоичный файл поставщика ресурсов MySQL и запустите самоизвлечение содержимого во временный каталог.

    >[!NOTE] 
    > Сборка поставщика ресурсов соответствует сборкам Azure Stack. Необходимо скачать надлежащий двоичный файл для выполняемой версии Azure Stack.

    | Сборка Azure Stack | Установщик MySQL RP |
    | --- | --- |
    | 1.0.180102.3 или 1.0.180106.1 (несколько узлов) | [MySQL RP версии 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1.0.171122.1 | [MySQL RP версии 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1.0.171028.1 | [MySQL RP версии 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  Корневой сертификат Azure Stack можно получить из привилегированной конечной точки. Для ASDK в рамках этого процесса создается самозаверяющий сертификат. Для системы с несколькими узлами вам нужно предоставить подходящий сертификат.

    Если нужно предоставить собственный сертификат, необходимо разместить PFX-файл в параметре **DependencyFilesLocalPath** (см. ниже) следующим образом:

    - Групповой сертификат для \*.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\> или сертификат для одного сайта с общим именем mysqladapter.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\>
    - Этот сертификат должен быть доверенным, например выданным центром сертификации. То есть должна существовать цепочка доверия без промежуточных сертификатов.
    - В DependencyFilesLocalPath имеется только один файл сертификата.
    - Имя файла не должно содержать специальные знаки.



5. Откройте **новую** консоль PowerShell с повышенными привилегиями (с правами администратора) и перейдите к каталогу, в который вы ранее извлекли файлы. Откройте новое окно, чтобы избежать проблем с неверными модулями PowerShell, уже установленными в системе.

6. [Установите Azure PowerShell версии 1.2.11](azure-stack-powershell-install.md).

7. Запустите скрипт DeploySqlProvider.ps1.

Этот скрипт выполняет следующие действия:

* Скачивает двоичный файл соединителя MySQL (его можно указать на автономном источнике).
* Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack.
* Публикует пакеты коллекции, что позволяет развертывать базу данных SQL из коллекции.
* Публикует пакет коллекции для развертывания серверов размещения.
* Развертывает виртуальную машину из образа Windows Server 2016, который вы создали на шаге 1, и устанавливает поставщик ресурсов.
* Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
* Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager (для клиента и администратора).


Вы можете:
- Указать по крайней мере обязательные параметры в командной строке.
- Или, если вы не указали параметры, ввести их при появлении запроса.

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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privleged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files
# Find the ERCS01 IP address first and make sure the certificate
# file is in the specified directory
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>Параметры DeploySqlProvider.ps1
Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые данные.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _обязательный параметр_ |
| **AzCredential** | Укажите учетные данные для записи администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _обязательный параметр_ |
| **VMLocalCredential** | Укажите учетные данные локального администратора на виртуальной машине поставщика ресурсов MySQL. | _обязательный параметр_ |
| **PrivilegedEndpoint** | Укажите IP-адрес или DNS-имя привилегированной конечной точки. |  _обязательный параметр_ |
| **DependencyFilesLocalPath** | Путь к локальному общему ресурсу, где содержится файл [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Если вы указали его, в этот каталог нужно поместить и файл сертификата. | _необязательно_ (_обязательно_, если в системе несколько узлов) |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _обязательный параметр_ |
| **MaxRetryCount** | Укажите, сколько раз нужно повторять каждую операцию в случае сбоя.| 2 |
| **RetryDuration** | Укажите время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаление поставщика ресурсов и всех связанных с ним ресурсов (см. примечания ниже) | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае ошибки. | Нет  |
| **AcceptLicense** | Пропуск запроса на принятие условий лицензии GPL (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html). | |


Установка может занять всего 20 минут или длиться несколько часов, в зависимости от производительности системы и скорости скачивания. Если колонка MySQLAdapter недоступна, обновите портал администрирования.

> [!NOTE]
> Если установка занимает более 90 минут, может произойти сбой. На экране и в файле журнала отобразится сообщение об ошибке. Развертывание возобновится с этапа, на котором произошел сбой. Если система не соответствует рекомендуемым характеристикам для памяти и основных компонентов, в ней невозможно развернуть поставщик ресурсов MySQL.



## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Проверка развертывания с использованием портала Azure Stack

> [!NOTE]
>  После выполнения скрипта установки необходимо обновить портал, чтобы появилась колонка администратора.


1. Войдите на портал администрирования в качестве администратора служб.

2. Проверьте, успешно ли выполнено развертывание. Найдите **Группы ресурсов**&gt;, щелкните группу **system.\<location\>.mysqladapter** и убедитесь, что все четыре развертывания завершены успешно.

      ![Проверка развертывания поставщика ресурсов MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Указание емкости путем подключения к серверу размещения MySQL

1. Войдите на портал Azure Stack в качестве администратора служб.

2. Щелкните **Administrative resources (Административные ресурсы)** &gt; **MySQL Hosting Servers (Серверы размещения MySQL)** &gt; **+Добавить**.

    В колонке **MySQL Hosting Servers** (Серверы размещения MySQL) можно подключить поставщик ресурсов MySQL Server к фактическим экземплярам MySQL Server, которые выступают в качестве серверной части поставщика ресурсов.

    ![Серверы размещения](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Заполните форму информацией о подключении своего экземпляра MySQL Server. Укажите полное доменное имя (FQDN) или допустимый IPv4-адрес и не короткое имя виртуальной машины. В этой версии больше не содержится экземпляр MySQL по умолчанию. Укажите размер, чтобы упростить управление емкостью базы данных для поставщика ресурсов. Его значение должно быть близким к физической емкости сервера базы данных.

    > [!NOTE]
    > Пока экземпляр MySQL может быть доступен Azure Resource Manager клиента и администратора, он может находиться под контролем поставщика ресурсов. Экземпляр MySQL __должен__ быть выделен исключительно для поставщика ресурсов.

4. При добавлении серверов им необходимо присваивать новые или имеющиеся номера SKU, чтобы различать предложения служб.
  Например, у вас может быть корпоративный экземпляр, обеспечивающий:
    - емкость базы данных;
    - автоматическое резервное копирование;
    - резервирование высокопроизводительных серверов для отдельных подразделений.
 

Название SKU должно отражать свойства, чтобы клиенты могли размещать свои базы данных соответствующим образом. У всех серверов размещения под одним номером SKU должны быть одинаковые возможности.

![Создание SKU MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> Отображение номеров SKU на портале может занять до часа. Вы не сможете создать базу данных, пока не будет создан номер SKU.


## <a name="to-test-your-deployment-create-your-first-mysql-database"></a>Чтобы протестировать развертывание, создайте первую базу данных MySQL.


1. Войдите на портал Azure Stack в качестве администратора служб.

2. Нажмите кнопку **+ Создать** &gt; **Данные+хранилище** &gt; **База данных MySQL**.

3. Заполните форму подробными сведениями о базе данных.

    ![Создание тестовой базы данных MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Выберите SKU.

    ![Выбор SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Создайте набор параметров для входа. Можно создать или повторно использовать старый набор. В нем содержатся такие параметры, как имя пользователя и пароль базы данных.

    ![Создание набора параметров для входа в базу данных](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    В строке подключения содержится имя сервера реальной базы данных. Скопируйте его на портале.

    ![Получение строки подключения для базы данных MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> Имя пользователя не может быть больше 32 символов в длину при использовании версии MySQL 5.7 или 16 символов — для более ранних версий.


## <a name="add-capacity"></a>Добавление емкости

Увеличьте емкость, подключив новые серверы MySQL на портале Azure Stack. Дополнительные серверы можно добавить к новому или существующему SKU. Убедитесь, что у серверов одинаковые характеристики.


## <a name="make-mysql-databases-available-to-tenants"></a>Предоставление клиентам доступа к базам данных MySQL
Создавайте планы и предложения, чтобы сделать базы данных MySQL доступными для клиентов. Для этого добавьте службу Microsoft.MySqlAdapter, квоту и т. д.

![Создание планов и предложений для включения в них баз данных](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Обновление пароля администратора
Пароль можно изменить, но сначала это нужно сделать в экземпляре сервера MySQL. Щелкните **ADMINISTRATIVE RESOURCES (Административные ресурсы)** &gt; **MySQL Hosting Servers (Серверы размещения MySQL)** &gt;, а затем сервер размещения. На панели "Параметры" щелкните "Пароль".

![Обновление пароля администратора](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Обновление адаптера поставщика ресурсов MySQL (только с несколькими узлами, сборка 1710 и более поздние версии)
При обновлении сборки Azure Stack будет выпущен новый адаптер поставщика ресурсов MySQL. Хотя имеющийся адаптер может продолжать работать, рекомендуется как можно быстрее обновить его до последней сборки после обновления Azure Stack. Процесс обновления очень похож на процесс установки, описанный выше. Будет создана виртуальная машина с последним RP-кодом. Параметры будут перенесены в этот новый экземпляр, включая базу данных и сведения о размещении сервера, а также необходимую запись DNS.

Используйте сценарий UpdateMySQLProvider.ps1 с аргументами, описанными выше. Здесь также необходимо указать сертификат.

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
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>Параметры UpdateMySQLProvider.ps1
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
| **AcceptLicense** | Пропуск запроса на принятие условий лицензии GPL (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html). | |

## <a name="remove-the-mysql-resource-provider-adapter"></a>Удаление адаптера поставщика ресурсов MySQL

Чтобы удалить поставщик ресурсов, сначала нужно удалить все его зависимости.

1. Найдите исходный пакет развертывания, который вы скачали ранее для своей версии поставщика ресурсов.

2. В поставщике ресурсов необходимо удалить все клиентские базы данных (сами данные при этом не удаляются). Эти действия должны выполнить клиенты.

3. Клиентам необходимо отменить регистрацию в пространстве имен.

4. Администратору следует удалить серверы размещения из адаптера MySQL.

5. Администратору необходимо удалить все планы, которые ссылаются на этот адаптер MySQL.

6. Администратору следует удалить все квоты, связанные с этим адаптером MySQL.

7. Снова запустите скрипт развертывания с параметром -Uninstall и укажите конечные точки Azure Resource Manager, DirectoryTenantID и учетные данные учетной записи администратора служб.




## <a name="next-steps"></a>Дополнительная информация

Изучите другие [услуги PaaS](azure-stack-tools-paas-services.md), такие как [поставщик ресурсов SQL Server](azure-stack-sql-resource-provider-deploy.md) или [поставщик ресурсов службы приложений](azure-stack-app-service-overview.md).
