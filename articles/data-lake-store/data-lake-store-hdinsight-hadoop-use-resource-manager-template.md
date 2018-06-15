---
title: Создание HDInsight и Data Lake Store с помощью шаблонов Azure | Документация Майкрософт
description: Создание кластеров HDInsight для работы с Azure Data Lake Store с помощью шаблонов Azure Resource Manager
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 7f49b643550b1888e1b0a4a6a57bbe4d428c65ac
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625786"
---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Создание кластера HDInsight с Data Lake Store с помощью шаблона Azure Resource Manager
> [!div class="op_single_selector"]
> * [Использование портала](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Использование PowerShell (для хранилища по умолчанию)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Использование PowerShell (для дополнительного хранилища)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Использование Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Узнайте, как с помощью Azure PowerShell настроить кластер HDInsight с Azure Data Lake Store в качестве **дополнительного хранилища**.

В поддерживаемых типах кластеров Data Lake Store можно использовать в качестве хранилища по умолчанию или дополнительной учетной записи хранения. Если Data Lake Store используется как дополнительное хранилище, в этом случае в качестве учетной записи хранения по умолчанию для кластеров по-прежнему используется Azure Storage Blob (WASB). Кроме того, относящиеся к кластеру файлы (журналы и т. д.) записываются в хранилище по умолчанию, а данные, которые необходимо обработать, могут храниться в учетной записи Data Lake Store. Использование хранилища озера данных в качестве дополнительной учетной записи хранения не влияет на производительность или возможность выполнять чтение и запись в хранилище из кластера.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Использование Data Lake Store в качестве хранилища кластера HDInsight

Ниже приведены некоторые важные сведения об использовании HDInsight с Data Lake Store.

* Возможность создавать кластеры HDInsight с доступом к Data Lake Store в качестве хранилища по умолчанию поддерживается в HDInsight версий 3.5 и 3.6.

* Возможность создавать кластеры HDInsight с доступом к Data Lake Store в качестве дополнительного хранилища поддерживается в HDInsight версий 3.2, 3.4, 3.5 и 3.6.

В этой статье мы подготовим кластер Hadoop, в котором хранилище озера данных будет дополнительным хранилищем. Инструкции по созданию кластера Hadoop с Data Lake Store в качестве хранилища по умолчанию см. в статье [Создание кластера HDInsight с Data Lake Store с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 или более поздней версии**. Ознакомьтесь со статьей [Установка и настройка Azure PowerShell](/powershell/azure/overview).
* **Субъект-служба Azure Active Directory**. В этом учебнике приведены инструкции по созданию субъекта-службы в Azure AD. Однако, чтобы создать субъект-службу, необходимо быть администратором Azure AD. Если вы являетесь администратором Azure AD, то можете пропустить это предварительное требование и продолжить работу с учебником.

    **Если вы не являетесь администратором Azure AD**, то вы не сможете выполнить шаги, необходимые для создания субъекта-службы. В этом случае администратор Azure AD должен сначала создать субъект-службу, после чего вы сможете создать кластер HDInsight с Data Lake Store. При создании субъекта-службы также необходимо использовать сертификат, как описано в разделе [Create a service principal with certificate](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) (Создание субъекта-службы с сертификатом).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Создание кластера HDInsight с помощью Azure Data Lake Store
Шаблон Resource Manager и предварительные требования для использования шаблона см. в статье [Deploy a HDInsight Linux cluster with new Data Lake Store](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) (Развертывание кластера HDInsight Linux с помощью нового хранилища Data Lake Store) на GitHub. Следуйте инструкциям по этой ссылке, чтобы создать кластер HDInsight с Azure Data Lake Store в качестве дополнительного хранилища.

Инструкции по ссылке выше требуют PowerShell. Прежде чем начать работу с ними, обязательно войдите в учетную запись Azure. На своем компьютере откройте новое окно Azure PowerShell и введите следующий фрагмент кода. Когда вам будет предложено войти, введите учетные данные администратора или владельца подписки.

```
# Log in to your Azure account
Connect-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Отправка примера данных в Azure Data Lake Store
Шаблон Resource Manager создает новую учетную запись Data Lake Store и связывает ее с кластером HDInsight. Отправьте пример данных в Data Lake Store. Эти данные потребуются позже при выполнении заданий из кластера HDInsight для получения доступа к данным в хранилище озера данных. Указания по отправке данных см. в разделе [Отправка файла в Data Lake Store](data-lake-store-get-started-portal.md#uploaddata). Если у вас нет под рукой подходящих для этих целей данных, передайте папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Установка соответствующих списков управления доступом для примера данных
Чтобы обеспечить доступ к отправляемым примерам данных для кластера HDInsight, необходимо убедиться, что у приложения Azure AD, используемого для определения удостоверения между кластером HDInsight и Data Lake Store, есть доступ к файлу или папке, к которой вы пытаетесь получить доступ. Для этого сделайте следующее.

1. Найдите имя приложения Azure AD, связанного с кластером HDInsight и Data Lake Store. Чтобы найти его, можно открыть колонку кластера HDInsight, созданного с помощью шаблона Resource Manager, выбрать вкладку **Удостоверение кластера AAD** и просмотреть значение параметра **Service Principal Display Name** (Отображаемое имя субъекта-службы).
2. Теперь предоставьте этому приложению Azure AD доступ к файлу или папке, к которым необходимо получить доступ из кластера HDInsight. Чтобы установить подходящие списки управления доступом для файла или папки в Data Lake Store, см. статью [Защита данных, хранимых в хранилище озера данных](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Выполнение тестовых заданий в кластере HDInsight
После настройки кластера HDInsight выполните в нем тестовые задания, чтобы проверить, доступно ли ему хранилище озера данных. Для этого запустите образец задания Hive, создающего таблицу с данными, которые вы ранее отправили в хранилище озера данных.

В этом разделе вы подключитесь к кластеру HDInsight на платформе Linux по протоколу SSH и выполните пример запроса Hive. При использовании клиента Windows мы рекомендуем использовать служебную программу **PuTTY**, которую можно скачать на странице [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Дополнительные сведения об использовании PuTTY см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. После подключения запустите интерфейс командной строки Hive с помощью следующей команды:

   ```
   hive
   ```
2. Используя интерфейс командной строки, введите следующие инструкции, чтобы создать таблицу с именем **vehicles** с помощью примера данных в хранилище озера данных.

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Должен отобразиться результат, аналогичный приведенному ниже:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-store-using-hdfs-commands"></a>Доступ к хранилищу озера данных с помощью команд HDFS
Настроив в кластере HDInsight параметры для работы с хранилищем озера данных, используйте для доступа к хранилищу команды оболочки HDFS.

В этом разделе вы подключитесь к кластеру HDInsight на платформе Linux по протоколу SSH и выполните команды HDFS. При использовании клиента Windows мы рекомендуем использовать служебную программу **PuTTY**, которую можно скачать на странице [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Дополнительные сведения об использовании PuTTY см. в разделе [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

После подключения используйте следующую команду файловой системы HDFS для получения списка файлов в хранилище озера данных.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Эта команда должна показать файл, который вы ранее отправили в хранилище озера данных.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

С помощью команды `hdfs dfs -put` вы можете отправить в хранилище озера данных некоторые файлы, а затем с помощью команды `hdfs dfs -ls` проверить, успешно ли они передались.


## <a name="next-steps"></a>Дополнительная информация
* [Copy data from Azure Storage Blobs to Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Использование Data Lake Store с кластерами Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
