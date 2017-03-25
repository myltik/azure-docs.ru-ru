---
title: "Подключение Excel к Hadoop с помощью Power Query | Документация Майкрософт"
description: "Узнайте, как пользоваться преимуществами компонентов бизнес-аналитики и применять Power Query для Excel для получения доступа к данным, хранящимся в Azure HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 6407c371bc51461a05429fabaf38d3f9bc80d32c
ms.openlocfilehash: 26383db7a3fe6736fb739121dd545518784c098a
ms.lasthandoff: 02/07/2017


---
# <a name="connect-excel-to-hadoop-by-using-power-query"></a>Подключение Excel к Hadoop с помощью Power Query
Одной из ключевых особенностей решения Майкрософт для работы с данными большого объема является интеграция компонентов бизнес-аналитики Майкрософт с кластерами Hadoop в Azure HDInsight. Важнейшим примером этой интеграции является возможность подключения Excel к учетной записи хранения Azure, в которой хранятся данные, связанные с кластером Hadoop, с помощью Microsoft Power Query для надстройки Excel. В этой статье приводится пошаговое руководство по настройке и использованию Power Query для запроса данных, связанных с кластером Hadoop, который управляется с помощью HDInsight.

> [!NOTE]
> Хотя действия, описанные в этой статье, применимы к кластеру HDInsight под управлением Windows или Linux, Windows является обязательным для клиентской рабочей станции.
> 
> 

### <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этой статьей необходимо иметь следующее:

* **Кластер HDInsight**. Дополнительные сведения о настройке кластера см. в статье [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight][hdinsight-get-started].
* **Рабочая станция** под управлением Windows 7, Windows Server 2008 R2 или последующих версий операционной системы.
* **Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс**.

## <a name="install-power-query"></a>Установка Power Query
Power Query можно использовать для импорта данных из различных источников в Microsoft Excel, где можно задействовать мощные средства бизнес-аналитики, например PowerPivot и Power View. В частности Power Query может импортировать данные, которые были выведены или созданы заданием Hadoop, работающим в кластере HDInsignt.

Скачайте Microsoft Power Query для Excel из [Центра загрузки Майкрософт][powerquery-download] и установите.

## <a name="import-hdinsight-data-into-excel"></a>Импорт данных HDInsight в Excel
Надстройка Power Query для Excel удобна для импорта данных из кластера HDInsight в Excel, где можно использовать средства бизнес-аналитики, такие как PowerPivot и Power Map, для изучения, анализа и представления данных.

**Импорт данных из кластера HDInsight**

1. Откройте Excel.
2. Создайте новую пустую книгу.
3. Откройте меню **Power Query**, щелкните **Из Azure**, а затем — **Из Microsoft Azure HDInsight**.
   
    ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
   
    **Примечание.** Если меню **Power Query** не отображается, последовательно выберите пункты **Файл** > **Параметры** > **Надстройки**, затем в раскрывающемся списке **Управление** в нижней части страницы затем выберите **Надстройки COM**. Нажмите кнопку **Перейти...** и убедитесь, что установлен флажок «Power Query для Excel».
   
    **Примечание.** Power Query также позволяет импортировать данные из HDFS, щелкнув **Из других источников**.
4. В поле **Имя учетной записи** введите имя учетной записи хранения больших двоичных объектов Azure, связанной с вашим кластером, и нажмите кнопку **ОК**. Это может быть [учетная запись хранения по умолчанию](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) или связанная учетная запись хранения.  Формат имеет следующий вид — *https://<StorageAccountName>.blob.core.windows.net/*.
5. В поле **Ключ учетной записи** введите ключ для учетной записи хранения больших двоичных объектов Azure, а затем нажмите кнопку **Сохранить**. (Необходимо сделать это при первом доступе к хранилищу.)
6. В области **Навигатор** слева от окна редактора запросов дважды щелкните имя контейнера хранилища больших двоичных объектов. По умолчанию имя контейнера совпадает с именем кластера.
7. Найдите **HiveSampleData.txt** в столбце **Имя** (путь папки: **../hive/warehouse/hivesampletable/**), а затем щелкните **Двоичный код** в левой части HiveSampleData.txt. HiveSampleData.txt поставляется вместе с кластером. При необходимости можно использовать собственный файл.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Если необходимо, можно переименовать имена столбцов. Когда будете готовы, щелкните **Закрыть и загрузить**.  Данные загружены в книгу.
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Дальнейшие действия
В этой статье было показано, как использовать Power Query для извлечения данных из HDInsight в Excel. Аналогичным образом можно извлекать данные из HDInsight в SQL Azure. Можно также передавать данные в HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-ODBC]
* [Отправка данных в HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689

