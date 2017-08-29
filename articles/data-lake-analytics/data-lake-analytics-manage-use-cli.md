---
title: "Управление Azure Data Lake Analytics с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "Узнайте, как управлять учетными записями, источниками данных, пользователями и заданиями аналитики озера данных Azure с помощью интерфейса командной строки Azure."
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: f90bada3572c0ed40b07d76ec02c1b499bbd1428
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Управление аналитикой озера данных Azure с помощью интерфейса командной строки (CLI) Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Узнайте, как управлять учетными записями, источниками данных, пользователями и заданиями Azure Data Lake Analytics с помощью Azure CLI. Для просмотра статей, посвященных управлению с помощью других инструментов, щелкните селектор вкладок выше.


**Предварительные требования**

Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Интерфейс командной строки Azure**. См. статью [Установка и настройка интерфейса командной строки Azure](../cli-install-nodejs.md).
  * Для выполнения этой демонстрации загрузите и установите **предварительный выпуск** [программ CLI Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) .
* **Пройдите проверку подлинности**с помощью следующей команды:
  
        azure login
    Дополнительные сведения об аутентификации с помощью рабочей или учебной учетной записи см. в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md).
* **Переключитесь в режим диспетчера ресурсов Azure**с помощью следующей команды.
  
        azure config mode arm

**Получение списка команд хранилища озера данных и аналитики озера данных**

    azure datalake store
    azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>Управление учетными записями
Перед выполнением любого задания аналитики озера данных необходимо иметь учетную запись аналитики озера данных. В отличие от Azure HDInsight учетная запись аналитики не оплачивается, если ни одно задание не выполняется.  Вы платите только за время, когда выполняется задание.  Дополнительные сведения см. в разделе [Обзор аналитики озера данных Azure](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Создание учетных записей
      azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


### <a name="update-accounts"></a>Обновление учетных записей
Следующая команда используется для обновления свойств существующей учетной записи данных озера аналитики.

    azure datalake analytics account set "<Data Lake Analytics Account Name>"


### <a name="list-accounts"></a>Список учетных записей
Список учетных записей аналитики озера данных 

    azure datalake analytics account list

Список учетных записей аналитики озера данных в конкретной группе ресурсов

    azure datalake analytics account list -g "<Azure Resource Group Name>"

Получение сведений о конкретной учетной записи аналитики озера данных

    azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

### <a name="delete-data-lake-analytics-accounts"></a>Удаление учетных записей аналитики озера данных
      azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Управление источниками данных учетной записи
Аналитика озера данных в настоящее время поддерживает следующие источники данных:

* [Хранилище озера данных Azure](../data-lake-store/data-lake-store-overview.md)
* [Хранилище Azure](../storage/common/storage-introduction.md)

При создании учетной записи аналитики необходимо указать учетную запись хранения озера данных Azure в качестве учетной записи хранения по умолчанию. Учетная запись хранения озера данных по умолчанию используется для хранения метаданных задания и журналов аудита задания. После создания учетной записи аналитики можно добавить дополнительные учетные записи хранения озера данных и учетные записи хранения Azure. 

### <a name="find-the-default-adl-storage-account"></a>Поиск учетной записи хранения озера данных по умолчанию
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

Значение находится в списке свойств: datalakeStoreAccount:name.

### <a name="add-additional-azure-blob-storage-accounts"></a>Добавление дополнительных учетных записей хранения больших двоичных объектов Azure
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

> [!NOTE]
> Поддерживаются только короткие имена хранилищ BLOB-объектов.  Не следует использовать полное доменное имя, например "myblob.blob.core.windows.net".
> 
> 

### <a name="add-additional-data-lake-store-accounts"></a>Добавление дополнительных учетных записей хранения озера данных
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] — это необязательный параметр, указывающий, является ли добавляемое озеро данных учетной записью озера данных по умолчанию. 

### <a name="update-existing-data-source"></a>Обновление существующего источника данных
Задание существующей учетной записи хранилища озера данных в качестве учетной записи по умолчанию

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d

Обновление существующего ключа учетной записи хранилища BLOB-объектов

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### <a name="list-data-sources"></a>Список источников данных
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

![Источник данных списка аналитики озера данных](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Удаление источников данных
Удаление учетной записи хранения озера данных Azure

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

Удаление учетной записи хранения BLOB-объектов

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## <a name="manage-jobs"></a>Управление заданиями
Для создания любого задания требуется учетная запись аналитики озера данных.  Дополнительные сведения см. в разделе [Управление учетными записями Data Lake Analytics](#manage-accounts).

### <a name="list-jobs"></a>Список заданий
      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![Источник данных списка аналитики озера данных](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Получение сведений о задании
      azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"

### <a name="submit-jobs"></a>Отправка заданий
> [!NOTE]
> Приоритет задания по умолчанию — 1000, а степень параллелизма по умолчанию для задания — 1.
> 
> 

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### <a name="cancel-jobs"></a>Отмена задания
Используйте команду list, чтобы найти идентификатор задания, а затем используйте команду cancel для отмены задания.

      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
      azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## <a name="manage-catalog"></a>Управление каталогом
Каталог U-SQL используется для структурирования данных и кода, чтобы их могли совместно использовать сценарии U-SQL. Каталог обеспечивает максимальную производительность, возможную с данными в озере данных Azure. Дополнительные сведения см. в разделе [Использование каталога U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-catalog-items"></a>Список элементов каталога
    #List databases
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

    #List tables
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table

К типа относятся база данных, схема, сборка, внешний источник данных, таблица, функция с табличным значением или статистика таблицы.

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-arm-groups"></a>Использование групп ARM
Обычно приложения состоят из множества компонентов, например веб-приложения, базы данных, сервера базы данных, хранилища и служб сторонних поставщиков. Вы можете развертывать, обновлять, отслеживать или удалять все ресурсы для приложения в рамках одной скоординированной операции. Для развертывания вы используете шаблон, который можно использовать для разных сред, в том числе для тестовой, промежуточной и рабочей. Вы можете уточнить счета для своей организации, просмотрев сведенные затраты для всей группы. Дополнительные сведения см. в статье [Обзор диспетчера ресурсов Azure](../azure-resource-manager/resource-group-overview.md). 

Служба аналитики озера данных может включать следующие компоненты:

* Учетная запись аналитики озера данных Azure
* Требуемая учетная запись хранения озера данных Azure по умолчанию
* Дополнительные учетные записи хранения озера данных Azure
* Дополнительные учетные записи хранения Azure

Можно создать все эти компоненты в одной группе ARM, чтобы ими было проще управлять.

![Аналитика озера данных Azure: учетная запись и хранилище](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Учетная запись аналитики озера данных и зависимые учетные записи хранения должны находиться в одном центре обработки данных Azure.
Однако группа ARM может находиться в другом центре обработки данных.  

## <a name="see-also"></a>Дополнительные материалы
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Начало работы с аналитикой озера данных с помощью портала Azure](data-lake-analytics-get-started-portal.md)
* [Управление аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-manage-use-portal.md)
* [Мониторинг заданий аналитики озера данных Azure и устранение связанных с ними неполадок с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)


