---
title: "PowerShell: кластер Azure HDInsight с Data Lake Store в качестве хранилища по умолчанию | Документация Майкрософт"
description: "Создание кластеров HDInsight для работы с озером данных Azure с помощью Azure PowerShell"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 01c75f20909c7334981bf407e775656476972276
ms.lasthandoff: 03/03/2017


---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-default-storage"></a>Создание кластера HDInsight с Data Lake Store (как хранилище по умолчанию) с помощью Azure PowerShell
> [!div class="op_single_selector"]
> * [Использование портала](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Использование PowerShell (для хранилища по умолчанию)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Использование PowerShell (для дополнительного хранилища)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Использование Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Узнайте, как с помощью Azure PowerShell настроить кластер HDInsight с Azure Data Lake Store в качестве **хранилища по молчанию**. Инструкции по созданию кластера HDInsight с Azure Data Lake Store в качестве дополнительного хранилища см. в статье [Создание кластера HDInsight с Data Lake Store (как дополнительное хранилище) с помощью Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md).

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Использование Data Lake Store в качестве хранилища кластера HDInsight

Ниже приведены некоторые важные сведения об использовании HDInsight с Data Lake Store.

* Возможность создавать кластеры HDInsight с доступом к Data Lake Store в качестве хранилища по умолчанию поддерживается для версии HDInsight 3.5.

* Возможность создавать кластеры HDInsight с доступом к Data Lake Store в качестве хранилища по умолчанию не поддерживается для кластеров HDInsight уровня "Премиум".

* В кластерах HBase (Windows и Linux) Data Lake Store **нельзя** использовать как хранилище по умолчанию, а также как дополнительное хранилище.


Настройка в HDInsight хранилища озера данных с помощью PowerShell состоит из нескольких этапов:

* создание хранилища озера данных Azure;
* настройка проверки подлинности для доступа к хранилищу озера данных на основе ролей;
* создание кластера HDInsight с проверкой подлинности в хранилище озера данных;
* выполнение тестового задания в кластере.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 или более поздней версии**. Ознакомьтесь со статьей [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).
* **Пакет SDK Windows**. Его можно установить [отсюда](https://dev.windows.com/en-us/downloads). Пакет используется для создания сертификата безопасности.
* **Субъект-служба Azure Active Directory**. В этом учебнике приведены инструкции по созданию субъекта-службы в Azure AD. Однако, чтобы создать субъект-службу, необходимо быть администратором Azure AD. Если вы являетесь администратором Azure AD, то можете пропустить это предварительное требование и продолжить работу с учебником.

    **Если вы не являетесь администратором Azure AD**, то вы не сможете выполнить шаги, необходимые для создания субъекта-службы. В этом случае администратор Azure AD должен сначала создать субъект-службу, после чего вы сможете создать кластер HDInsight с Data Lake Store. При создании субъекта-службы также необходимо использовать сертификат, как описано в разделе [Create a service principal with certificate](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) (Создание субъекта-службы с сертификатом).

## <a name="create-an-azure-data-lake-store"></a>Создание хранилища озера данных Azure
Чтобы создать хранилище озера данных, сделайте следующее.

1. На своем компьютере откройте новое окно Azure PowerShell и введите следующий фрагмент кода. Когда вам будет предложено войти, введите учетные данные администратора или владельца подписки.

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Если при регистрации поставщика ресурсов хранилища озера данных появляется сообщение об ошибке, похожее на `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, это может означать, что ваша подписка отсутствует в списке разрешений для хранилища озера данных Azure. Убедитесь, что подписка Azure для общедоступной предварительной версии Data Lake Store включена, выполнив указанные [инструкции](data-lake-store-get-started-portal.md).
   >
   >
2. Учетная запись хранения озера данных Azure связывается с группой ресурсов Azure. Для начала создайте группу ресурсов Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Создание группы ресурсов Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Создание группы ресурсов Azure")
3. Создайте учетную запись хранилища озера данных Azure. Имя новой учетной записи должно содержать только строчные буквы и цифры.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Создание учетной записи Azure Data Lake](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Создание учетной записи Azure Data Lake")
4. Убедитесь, что учетная запись создана.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Результат должен иметь значение **True**.

5. При использовании Data Lake Store в качестве хранилища по умолчанию необходимо указать путь к корневой папке, в которую будут скопированы связанные с кластером файлы в процессе создания кластера. Используйте приведенные ниже командлеты, чтобы создать корневой путь (в следующем фрагменте это — **/clusters/hdiadlcluster**).

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>настройка проверки подлинности для доступа к хранилищу озера данных на основе ролей;
Каждая подписка Azure связана со службой Azure Active Directory. Пользователи и службы, получающие доступ к ресурсам подписки через классический портал Azure или API Azure Resource Manager, сначала должны пройти аутентификацию в соответствующей службе Azure Active Directory. Доступ к подпискам и службам Azure предоставляется путем назначения соответствующей роли для ресурса Azure.  В случае со службами субъект-служба идентифицирует службу в Azure Active Directory (AAD). В этом разделе мы расскажем, как предоставить службе приложения, в частности HDInsight, доступ к ресурсу Azure (созданной ранее учетной записи хранилища озера данных Azure). Для этого с помощью Azure PowerShell мы создадим для приложения субъект-службу и назначим ему роли.

Чтобы настроить для озера данных Azure проверку подлинности в Active Directory, вам необходимо сделать следующее:

* Создание самозаверяющего сертификата
* создать приложение в Azure Active Directory и субъект-службу.

### <a name="create-a-self-signed-certificate"></a>Создание самозаверяющего сертификата
Прежде чем выполнять дальнейшие действия, описанные в этом разделе, убедитесь, что на вашем компьютере установлен [пакет SDK Windows](https://dev.windows.com/en-us/downloads). Вам также необходимо создать каталог, например **C:\mycertdir**, в котором будет создан сертификат.

1. В окне PowerShell перейдите в расположение, где установлен пакет SDK Windows (обычно это `C:\Program Files (x86)\Windows Kits\10\bin\x86`), и с помощью служебной программы [MakeCert][makecert] создайте самозаверяющий сертификат и закрытый ключ. Используйте такие команды:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        
        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Вам будет предложено ввести пароль для закрытого ключа. После успешного выполнения команды в указанном каталоге сертификатов должны появиться файлы **CertFile.cer** и **mykey.pvk**.
2. С помощью служебной программы [Pvk2Pfx][pvk2pfx] преобразуйте созданные программой MakeCert PVK- и CER-файлы в PFX-файл. Выполните следующую команду:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    При появлении соответствующего запроса введите указанный ранее пароль для закрытого ключа. Значение, указываемое для параметра **-po** — это пароль, связанный с PFX-файлом. После успешного выполнения команды вы должны увидеть в указанном каталоге сертификата файл CertFile.pfx.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Создание приложения в Azure Active Directory и субъекта-службы
Следуя описаниям, приведенным в этом разделе, вы создадите субъект-службу для приложения Azure Active Directory, назначите роль субъекту-службе и пройдете проверку подлинности в качестве субъекта-службы, используя сертификат. Чтобы создать приложение в Azure Active Directory, выполните следующие команды.

1. В окне консоли PowerShell вставьте из буфера обмена следующие командлеты. Укажите для свойства **-DisplayName** уникальное значение. Значения свойств **-HomePage** b **-IdentiferUris** — это заполнители, которые не проверяются.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. С помощью идентификатора приложения создайте субъект-службу.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Предоставьте субъекту-службе доступ к корневой папке Data Lake Store и всем папкам в ранее указанном корневом пути. Используйте приведенные ниже командлеты.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-default-storage"></a>Создание кластера HDInsight на платформе Linux с Data Lake Store в качестве хранилища по умолчанию

В этом разделе показано, как создать кластер HDInsight Hadoop на платформе Linux с Data Lake Store в качестве хранилища по умолчанию. В этом выпуске кластер HDInsight и Data Lake Store должны быть в одном расположении.

1. Сначала получите идентификатор клиента подписки. Позже он вам понадобится.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
        
2. Создайте кластер HDInsight, выполнив следующие командлеты.

        # Set these variables
        
        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                          # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.5" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    В случае успешного выполнения командлета должен отобразиться список сведений о кластере.

        
## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Выполнение тестовых заданий в кластере HDInsight
После настройки кластера HDInsight выполните в нем тестовые задания, чтобы проверить, доступно ли ему хранилище озера данных. Для этого запустите образец задания Hive, создающего таблицу с данными, которые уже доступны в Data Lake Store в расположении **<cluster root>/example/data/sample.log**.

В этом разделе вы подключитесь к созданному кластеру HDInsight под управлением Linux по протоколу SSH и выполните пример запроса Hive.

* Если вы используете клиент Windows для подключения к кластеру по протоколу SSH, ознакомьтесь со статьей [Использование SSH с HDInsight (Hadoop) в PuTTY на базе Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Если вы используете клиент Linux для подключения к кластеру по SSH, ознакомьтесь со статьей [Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. После подключения запустите интерфейс командной строки Hive с помощью следующей команды:

        hive
2. Используя интерфейс командной строки, введите следующие инструкции, чтобы создать таблицу с именем **vehicles** с помощью примера данных в хранилище озера данных.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Результаты запроса появятся в консоли SSH. 

    > [!NOTE]
       > Путь к демонстрационным данных в приведенной выше команде CREATE TABLE — `adl:///example/data/`, где `adl:///` является корнем кластера. Используя пример корня кластера, указанный в этом руководстве, получаем `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Таким образом, можно использовать более короткий вариант пути или указать полный путь к корню кластера. 
       >
       >



## <a name="access-data-lake-store-using-hdfs-commands"></a>Доступ к хранилищу озера данных с помощью команд HDFS
Настроив в кластере HDInsight параметры для работы с хранилищем озера данных, используйте для доступа к хранилищу команды оболочки HDFS.

В этом разделе вы подключитесь к созданному кластеру HDInsight под управлением Linux по протоколу SSH и выполните команды HDFS. 

* Если вы используете клиент Windows для подключения к кластеру по протоколу SSH, ознакомьтесь со статьей [Использование SSH с HDInsight (Hadoop) в PuTTY на базе Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Если вы используете клиент Linux для подключения к кластеру по SSH, ознакомьтесь со статьей [Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

После подключения используйте следующую команду файловой системы HDFS для получения списка файлов в хранилище озера данных.

    hdfs dfs -ls adl:///

С помощью команды `hdfs dfs -put` вы можете отправить в хранилище озера данных некоторые файлы, а затем с помощью команды `hdfs dfs -ls` проверить, успешно ли они передались.

## <a name="see-also"></a>См. также
* [Портал: создание кластера HDInsight для работы с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

