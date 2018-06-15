---
title: Создание кластеров HDInsight, использующих Data Lake Store, с помощью PowerShell | Документация Майкрософт
description: Создание и использование кластеров HDInsight, использующих Azure Data Lake Store, с помощью Azure PowerShell.
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 71b6b507952793e34a0e0413d7d652640680dab7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625711"
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>Создание кластеров HDInsight, использующих Data Lake Store, с помощью PowerShell

> [!div class="op_single_selector"]
> * [Использование портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Использование PowerShell (для хранилища по умолчанию)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Использование PowerShell (для дополнительного хранилища)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Использование Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Узнайте, как с помощью Azure PowerShell настроить кластер Azure HDInsight, использующий Azure Data Lake Store в качестве хранилища по молчанию. Инструкции по созданию кластера HDInsight, использующего Data Lake Store в качестве дополнительного хранилища, см. в статье [Создание кластера HDInsight с Data Lake Store (как дополнительное хранилище) с помощью Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md).

Ниже приведены некоторые важные сведения об использовании HDInsight с Data Lake Store.

* Возможность создавать кластеры HDInsight, использующие Data Lake Store в качестве хранилища по умолчанию, поддерживается для HDInsight версий 3.5 и 3.6.

* Возможность создавать кластеры HDInsight, использующие Data Lake Store в качестве хранилища по умолчанию, *не поддерживается* для кластеров HDInsight уровня "Премиум".

Чтобы настроить HDInsight для работы с Data Lake Store с помощью PowerShell, следуйте инструкциям в следующих пяти разделах.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем приступить к изучению этого руководства, убедитесь, что выполнены следующие требования.

* **Подписка на Azure**. Перейдите на сайт [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 или более поздняя версия**. Инструкции см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/overview).
* **Пакет средств разработки программного обеспечения для Windows (пакет SDK)**. Инструкции по установке пакета Windows SDK приведены на странице [Загружаемые файлы и инструменты для Windows 10](https://dev.windows.com/downloads). Пакет SDK используется для создания сертификата безопасности.
* **Субъект-служба Azure Active Directory**. В этом руководстве описывается создание субъекта-службы в Azure Active Directory (Azure AD). Чтобы создать субъект-службу, необходимо быть администратором Azure AD. Если вы являетесь администратором, то можете пропустить это предварительное требование и продолжить работу с руководством.

    >[!NOTE]
    >Создать субъект-службу может только администратор Azure AD. Администратор Azure AD должен сначала создать субъект-службу, после чего вы сможете создать кластер HDInsight, использующий Data Lake Store. При создании субъекта-службы необходимо использовать сертификат, как описано в разделе [Создание субъекта-службы с использованием сертификата](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-store-account"></a>Создание учетной записи хранения озера данных

Чтобы создать учетную запись Data Lake Store, выполните следующее.

1. На своем компьютере откройте окно PowerShell и введите приведенные ниже фрагменты кода. Когда будет предложено выполнить вход, войдите как один администраторов или владельцев из подписки. 

        # Sign in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Если вы регистрируете поставщик ресурсов Data Lake Store и видите сообщение об ошибке `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, возможно, ваша подписка не добавлена в список разрешений для Data Lake Store. Чтобы использовать подписку Azure для общедоступной предварительной версии Data Lake Store, следуйте инструкциям в разделе [Начало работы с хранилищем озера данных Azure с помощью портала Azure](data-lake-store-get-started-portal.md).
    >

2. Учетная запись Data Lake Store связывается с группой ресурсов Azure. Для начала создайте группу ресурсов.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Вы должны увидеть подобные выходные данные:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Создайте учетную запись Data Lake Store. Имя этой учетной записи должно содержать только строчные буквы и цифры.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    Вы должны увидеть подобные выходные данные:

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

4. При использовании Data Lake Store в качестве хранилища по умолчанию необходимо указать путь к корневой папке, в которую будут скопированы связанные с кластером файлы в процессе создания кластера. Используйте приведенные ниже командлеты, чтобы создать корневую папку (в следующем фрагменте это **/clusters/hdiadlcluster**).

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>настройка проверки подлинности для доступа к хранилищу озера данных на основе ролей;
Каждая подписка Azure связана с сущностью Azure AD. Пользователи и службы, обращающиеся к ресурсам подписки через портал Azure или API Azure Resource Manager, сначала должны пройти аутентификацию в Azure AD. Доступ к подпискам и службам Azure предоставляется путем назначения соответствующей роли для ресурса Azure. В случае служб субъект-служба идентифицирует службу в Azure AD.

В этом разделе показано, как предоставить службе приложения, например HDInsight, доступ к ресурсу Azure (учетной записи Data Lake Store, созданной ранее). Для этого следует создать субъект-службу для приложения и назначить ему роли с помощью PowerShell.

Чтобы настроить аутентификацию Active Directory для Azure Data Lake, необходимо выполнить инструкции, приведенные в двух следующих разделах.

### <a name="create-a-self-signed-certificate"></a>Создание самозаверяющего сертификата
Прежде чем выполнять дальнейшие действия, описанные в этом разделе, убедитесь, что на вашем компьютере установлен [пакет SDK Windows](https://dev.windows.com/en-us/downloads). Вам также необходимо создать каталог, например *C:\mycertdir*, для создания сертификата.

1. В окне PowerShell перейдите в расположение, где установлен пакет Windows SDK (обычно это *C:\Program Files (x86)\Windows Kits\10\bin\x86*), и с помощью служебной программы [MakeCert][makecert] создайте самозаверяющий сертификат и закрытый ключ. Используйте следующие команды:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Вам будет предложено ввести пароль для закрытого ключа. После успешного выполнения команды в указанном каталоге сертификатов должны появиться файлы **CertFile.cer** и **mykey.pvk**.
2. С помощью служебной программы [Pvk2Pfx][pvk2pfx] преобразуйте созданные программой MakeCert PVK- и CER-файлы в PFX-файл. Выполните следующую команду:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    При появлении соответствующего запроса введите указанный ранее пароль для закрытого ключа. Значение, указываемое для параметра **-po**, — это пароль, связанный с PFX-файлом. После успешного выполнения команды в указанном каталоге сертификата также должен появиться файл **CertFile.pfx**.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Создание приложения Azure AD и субъекта-службы
Следуя инструкциям в этом разделе, вы создадите субъект-службу для приложения Azure Active Directory, назначите роль субъекту-службе и пройдете аутентификацию в качестве субъекта-службы, используя сертификат. Чтобы создать приложение в Azure AD, выполните следующие команды.

1. В окне консоли PowerShell вставьте из буфера обмена следующие командлеты. Обязательно укажите для свойства **-DisplayName** уникальное значение. Значения свойств **-HomePage** b **-IdentiferUris** — это заполнители, которые не проверяются.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

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
3. Предоставьте субъекту-службе доступ к корневой папке Data Lake Store и всем папкам в ранее указанном корневом пути. Выполните приведенные ниже командлеты.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Создание кластера HDInsight на платформе Linux, использующего Data Lake Store в качестве хранилища по умолчанию

Следуя инструкциям в этом разделе, вы создадите кластер HDInsight Hadoop на платформе Linux, использующий Data Lake Store в качестве хранилища по умолчанию. При использовании этого выпуска кластер HDInsight и Data Lake Store должны находиться в одном расположении.

1. Получите идентификатор клиента для подписки и сохраните его для последующего использования.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Создайте кластер HDInsight с помощью приведенных ниже командлетов.

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
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
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    После успешного выполнения командлетов должны отобразиться сведения о кластере.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>Выполнение тестовых заданий в кластере HDInsight для использования Data Lake Store
После настройки кластера HDInsight выполните в нем тестовые задания, чтобы проверить, может ли он использовать Data Lake Store. Для этого выполните пример задания Hive, чтобы создать таблицу, использующую демонстрационные данные, которые уже доступны в Data Lake Store в расположении *<cluster root>/example/data/sample.log*.

Следуя инструкциям в этом разделе, вы подключитесь по протоколу Secure Shell (SSH) к созданному кластеру HDInsight на платформе Linux и выполните пример запроса Hive.

* Если вы используете клиент Windows для подключения к кластеру по протоколу SSH, ознакомьтесь со статьей [Использование SSH с HDInsight (Hadoop) в PuTTY на базе Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Если вы используете клиент Linux для подключения к кластеру по SSH, ознакомьтесь со статьей [Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Установив подключение, запустите интерфейс командной строки Hive, выполнив в командной строке следующую команду.

        hive
2. Используя интерфейс командной строки, введите приведенные ниже инструкции, чтобы создать таблицу **vehicles** с помощью демонстрационных данных в Data Lake Store.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Результаты запроса появятся в консоли SSH.

    >[!NOTE]
    >Путь к демонстрационным данных в приведенной выше команде CREATE TABLE — `adl:///example/data/`, где `adl:///` является корнем кластера. Используя пример корня кластера, указанный в этом руководстве, получаем команду `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Можно использовать более короткий вариант пути или указать полный путь к корню кластера.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>Доступ к Data Lake Store с помощью команд HDFS
Настроив кластер HDInsight для работы с Data Lake Store, можно использовать для доступа к хранилищу команды оболочки HDFS (распределенная файловая система Hadoop).

Следуя инструкциям в этом разделе, вы подключитесь по протоколу SSH к созданному кластеру HDInsight на платформе Linux и выполните команды HDFS.

* Если вы используете клиент Windows для подключения к кластеру по протоколу SSH, ознакомьтесь со статьей [Использование SSH с HDInsight (Hadoop) в PuTTY на базе Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Если вы используете клиент Linux для подключения к кластеру по SSH, ознакомьтесь со статьей [Использование SSH с HDInsight (Hadoop) на платформе Windows, Linux, Unix или OS X](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Когда подключение будет установлено, выведите список файлов в Data Lake Store, используя приведенную ниже команду файловой системы HDFS.

    hdfs dfs -ls adl:///

С помощью команды `hdfs dfs -put` вы можете передать несколько файлов в Data Lake Store, а затем с помощью команды `hdfs dfs -ls` проверить, успешно ли они передались.

## <a name="see-also"></a>См. также
* [Использование Data Lake Store с кластерами Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Создание кластера HDInsight с хранилищем озера данных с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
