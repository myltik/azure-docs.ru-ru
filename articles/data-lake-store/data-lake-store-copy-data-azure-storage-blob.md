---
title: "Копирование данных из больших двоичных объектов хранилища Azure в Data Lake Store | Документация Майкрософт"
description: "Использование средства AdlCopy для копирования данных из больших двоичных объектов хранилища Azure в хранилище озера данных"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/05/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8bb80209ac225e60f43c406052035c6fbbc545c2


---
# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Копирование данных из больших двоичных объектов хранилища Azure в хранилище озера данных
> [!div class="op_single_selector"]
> * [С помощью DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [С помощью AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
> 
> 

В Azure Data Lake Store доступно средство командной строки [AdlCopy](http://aka.ms/downloadadlcopy)для копирования данных из следующих источников.

* Из больших двоичных объектов службы хранилища Azure в Data Lake Store. AdlCopy нельзя использовать для копирования данных из хранилища озера данных в большие двоичные объекты хранилища Azure.
* Между двумя учетными записями хранения Azure Data Lake Store. 

Также можно использовать инструмент AdlCopy в двух различных режимах:

* **Автономный**, при котором для выполнения задачи используются ресурсы хранилища озера данных.
* **С помощью учетной записи аналитики озера данных**, при котором для выполнения операции копирования используются единицы, назначенные учетной записи аналитики озера данных. Этот вариант можно использовать, если вы хотите, чтобы задачи копирования выполнялись предсказуемым образом.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этой статьей необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* **больших двоичных объектов хранилища Azure** с некоторыми данными.
* **Учетная запись Azure Data Lake Analytics (необязательно).** Инструкции по созданию учетной записи Data Lake Store см. в статье [Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
* **Средство AdlCopy**. Установите средство AdlCopy по ссылке [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Синтаксис средства AdlCopy
Используйте следующий синтаксис для работы со средством AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

Параметры синтаксиса описаны ниже:

| Параметр | Описание |
| --- | --- |
| Источник |Указывает расположение исходных данных в хранилище больших двоичных объектов Azure. Источником может быть контейнер больших двоичных объектов, сам большой двоичный объект или другая учетная запись Data Lake Store. |
| Dest |Указывает целевое хранилище озера данных для копирования. |
| SourceKey |Указывает ключ доступа к хранилищу для источника — хранилища больших двоичных объектов Azure. Это необходимо только в том случае, если источником является контейнер больших двоичных объектов или сам большой двоичный объект. |
| Учетная запись |**Необязательно**. Используйте этот вариант, если хотите, чтобы для запуска задания копирования использовалась учетная запись аналитики озера данных Azure. Если при запуске средства указан параметр /Account, но не указана учетная запись аналитики озера данных Azure, то для запуска задания AdlCopy использует учетную запись по умолчанию. Кроме того, при использовании этого параметра необходимо добавить источник (хранилище больших двоичных Azure) и место назначения (хранилище озера данных Azure) в качестве источников данных для вашей учетной записи аналитики озера данных. |
| Units |Указывает количество единиц аналитики озера данных, которые будут использоваться для задания копирования. Этот параметр является обязательным, если вы указываете учетную запись аналитики озера данных с помощью параметра **/Account** . |
| Модель |Определяет шаблон регулярного выражения, который указывает, какие именно большие двоичные объекты и файлы нужно копировать. В AdlCopy используется сопоставление с учетом регистра. Если шаблон не указан, то по умолчанию используется копирование всех элементов. Задание нескольких шаблонов не поддерживается. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Использование AdlCopy (автономно) для копирования данных из большого двоичного объекта службы хранилища Azure
1. Откройте командную строку и перейдите в каталог, в который установлено средство AdlCopy, обычно `%HOMEPATH%\Documents\adlcopy`.
2. Выполните следующую команду, чтобы скопировать заданный большой двоичный объект из контейнера-источника в хранилище озера данных:
   
        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
   
    Например:
   
        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[AZURE.NOTE] Приведенный выше синтаксис указывает файл, который будет скопирован в папку в учетной записи Data Lake Store. Инструмент AdlCopy создает папку, если указанного имени папки не существует.

    Вам будет предложено ввести учетные данные для подписки Azure, в которой расположена учетная запись Data Lake Store. Вы увидите результат, аналогичный приведенному ниже:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Также можно скопировать все большие двоичные объекты из одного контейнера в учетную запись хранилища данных озера, используя следующую команду:
   
        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        
   
    Например:
   
        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Использование AdlCopy (автономно) для копирования данных из другой учетной записи Data Lake Store
Также можно использовать AdlCopy для копирования данных между двумя учетными записями Data Lake Store.

1. Откройте командную строку и перейдите в каталог, в который установлено средство AdlCopy, обычно `%HOMEPATH%\Documents\adlcopy`.
2. Чтобы скопировать определенный файл из одной учетной записи Data Lake Store в другую, выполните следующую команду.
   
        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/
   
    Например:
   
        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/
   
   > [!NOTE]
   > Приведенный выше синтаксис указывает файл, который будет скопирован в папку в целевой учетной записи Data Lake Store. Инструмент AdlCopy создает папку, если указанного имени папки не существует.
   > 
   > 
   
    Вам будет предложено ввести учетные данные для подписки Azure, в которой расположена учетная запись Data Lake Store. Вы увидите результат, аналогичный приведенному ниже:
   
        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. Следующая команда копирует все файлы из указанной папки в исходной учетной записи Data Lake Store в папку в целевой учетной записи Data Lake Store.
   
        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Использование AdlCopy (с учетной записью Data Lake Analytics) для копирования данных
Вы также можете воспользоваться своей учетной записью аналитики озера данных для запуска задания AdlCopy для копирования данных из хранилища больших двоичных объектов Azure в хранилище озера данных. Этот вариант обычно используется, если объем перемещаемых данных измеряется гигабайтами или терабайтами и вам необходима лучшая и прогнозируемая производительность.

Чтобы использовать учетную запись Data Lake Analytics с AdlCopy для копирования из большого двоичного объекта службы хранилища Azure, необходимо добавить источник (большой двоичный объект службы хранилища Azure) в качестве источника данных для вашей учетной записи Data Lake Analytics. Инструкции по добавлению дополнительных источников данных в учетную записи Data Lake Analytics см. в разделе [Управление источниками данных учетной записи](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

> [!NOTE]
> Если копирование выполняется с использованием учетной записи Data Lake Analytics, а источником является учетная запись Azure Data Lake Store, то учетную запись Data Lake Store не требуется связывать с учетной записью Data Lake Analytics. Связь исходного хранилища с учетной записью Data Lake Analytics требуется только в том случае, если источником является учетная запись хранения Azure.
> 
> 

Выполните следующую команду, чтобы скопировать данные из большого двоичного объекта службы хранилища Azure в учетную запись Data Lake Store, используя учетную запись Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Например:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


Аналогичным образом выполните следующую команду, чтобы скопировать данные из большого двоичного объекта службы хранилища Azure в учетную запись Data Lake Store, используя учетную запись Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Использование AdlCopy для копирования данных с помощью сопоставления шаблонов
В этом разделе показано, как использовать AdlCopy для копирования данных из источника (в приведенном ниже примере — из большого двоичного объекта службы хранилища Azure) в целевую учетную запись Data Lake Store с помощью сопоставления шаблонов. Например, описанные ниже действия можно использовать для копирования всех файлов с расширением CSV из исходного большого двоичного объекта в целевое хранилище.

1. Откройте командную строку и перейдите в каталог, в который установлено средство AdlCopy, обычно `%HOMEPATH%\Documents\adlcopy`.
2. Выполните следующую команду, чтобы скопировать все файлы с расширением CSV из указанного большого двоичного объекта из исходного контейнера в Data Lake Store:
   
        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv
   
    Например:
   
        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Выставление счетов
* При автономном использовании средства AdlCopy исходящий трафик для перемещения данных будет тарифицироваться в том случае, если источник учетной записи хранилища Azure и хранилище озера данных находятся в разных регионах.
* При использовании средства AdlCopy с вашей учетной записью аналитики озера будут применены стандартные [тарифные ставки аналитики озера данных](https://azure.microsoft.com/pricing/details/data-lake-analytics/) .

## <a name="considerations-for-using-adlcopy"></a>Рекомендации по использованию AdlCopy
* AdlCopy (для версии 1.0.5) поддерживает копирование данных из источников, которые совокупно содержат тысячи файлов и папок. Тем не менее, если при копировании большого набора данных возникают проблемы, то вы можете распределить файлы и папки в разных вложенных папках и использовать путь к этим вложенным папкам в качестве источника.

## <a name="next-steps"></a>Дальнейшие действия
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
* [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Nov16_HO3-->


