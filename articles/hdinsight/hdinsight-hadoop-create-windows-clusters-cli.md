---
title: Создание кластеров Hadoop под управлением Windows в HDInsight с помощью интерфейса командной строки Azure
description: Научитесь создавать кластеры для Azure HDInsight с помощью интерфейса командной строки Azure.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao

---
# Создание кластеров Hadoop под управлением Windows в HDInsight с помощью интерфейса командной строки Azure
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Научитесь создавать кластеры HDInsight с использованием интерфейса командной строки Azure Сведения о других инструментах и функциях создания кластера приведены на вкладке в верхней части этой страницы или в разделе [Способы создания кластера](hdinsight-provision-clusters.md#cluster-creation-methods).

## Предварительные требования:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Прежде чем следовать указаниям в этой статье, необходимо подготовить следующее:

* **Подписка Azure**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure CLI**.
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

## Подключение к Azure
Подключитесь к Azure, выполнив следующую команду:

    azure login

Дополнительную информацию об аутентификации с помощью рабочей или учебной учетной записи см. в разделе [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md).

Выполните следующую команду, чтобы переключиться в режим ARM:

    azure config mode arm

Чтобы получить справку, используйте параметр **-h**. Например:

    azure hdinsight cluster create -h

## Создание кластеров
Для создания кластера HDInsight необходимо иметь службу управления ресурсами Azure (ARM) и учетную запись хранилища больших двоичных объектов Azure. Чтобы создать кластер HDInsight, необходимо указать следующее:

* **Группа ресурсов Azure**: необходимо создать учетную запись аналитики озера данных в группе "Ресурс Azure". Диспетчер ресурсов Azure позволяет вам работать с ресурсами в приложении в виде группы. Вы можете развертывать, обновлять или удалять все ресурсы для приложения в рамках одной скоординированной операции.
  
    Для перечисления групп ресурсов в своей подписке выполните следующие действия.
  
        azure group list
  
    Чтобы создать новую группу ресурсов:
  
        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Имя кластера HDInsight.**
* **Расположение**. Один из центров обработки данных Azure, который поддерживает кластеры HDInsight. Список поддерживаемых кластеров см. в разделе [Цены на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
* **Учетная запись хранения по умолчанию**. Кластер HDInsight использует контейнер хранилища больших двоичных объектов Azure в качестве файловой системы по умолчанию. Для создания кластера HDInsight требуется учетная запись хранения Azure.
  
    Создание новой учетной записи хранения Azure
  
        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS
  
  > [!NOTE]
  > Учетная запись хранения должна находиться вместе с HDInsight в том же центре обработки данных. Тип учетной записи хранилища не может быть ZRS, так как ZRS не поддерживает таблицы.
  > 
  > 
  
    Сведения о создании новой учетной записи хранения Azure с помощью портала Azure см. в статье [Создание, управление и удаление учетной записи хранения][azure-create-storageaccount].
  
    Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:
  
        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"
  
    Дополнительные сведения о получении информации с помощью портала Azure см. в разделе "Управление учетной записью хранения" статьи [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md#manage-your-storage-account).
* **(Необязательно) Контейнер больших двоичных объектов по умолчанию**. Команда **azure hdinsight cluster create** создает контейнер, если таковой еще не существует. Если контейнер создан заранее, можно использовать следующую команду:
  
    azure storage container create --account-name "<имя учетной записи хранения>" --account-key <ключ учетной записи хранения> [ContainerName]

После создания учетной записи хранения все готово к созданию кластера.

    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


## Создание кластеров с помощью файлов конфигурации
Как правило, необходимо создать кластер HDInsight, выполнить в нем задания, а затем удалить кластер для сокращения расходов. Интерфейс командной строки предоставляет возможность сохранения конфигураций в файле, который можно повторно использовать при каждом создании кластера.

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

Пример. Создание файла конфигурации, содержащего действие сценария, выполняемое при создании кластера.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

## Создание кластеров с помощью действия сценария
Создание файла конфигурации, содержащего действие сценария, выполняемое при создании кластера.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Создание кластера с помощью действия сценария

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


Общую информацию о действиях сценария см. в статье [Настройка кластеров HDInsight с помощью действия скрипта (Linux)](hdinsight-hadoop-customize-cluster.md).

## Создание кластеров с помощью шаблонов ARM
Интерфейс командной строки можно использовать для создания кластеров с помощью вызова шаблонов ARM. См. статью [Развертывание с помощью интерфейса командной строки Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## Дополнительные материалы
* [Приступая к работе с Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) — узнайте, как начать работу с кластером HDInsight.
* [Отправка заданий Hadoop программными средствами](hdinsight-submit-hadoop-jobs-programmatically.md) — узнайте, как программными средствами отправлять задания в HDInsight.
* [Управление кластерами Hadoop в HDInsight с помощью интерфейса командной строки (CLI) Azure](hdinsight-administer-use-command-line.md)
* [Управление службами Azure с помощью интерфейса командной строки Azure на компьютерах Mac, а также на компьютерах с ОС Linux и Windows](../virtual-machines-command-line-tools.md)

<!---HONumber=AcomDC_0914_2016-->