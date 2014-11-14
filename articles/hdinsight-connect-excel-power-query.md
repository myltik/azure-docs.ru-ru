<properties urlDisplayName="HDInsight and Excel" pageTitle="Подключение Excel к Hadoop с помощью Power Query | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="Узнайте, как эффективно использовать компоненты бизнес-аналитики и Excel для получения доступа к данным, хранящимся в Azure HDInsight, с помощью Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Подключение Excel к Hadoop с помощью Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Подключение Excel к Hadoop с помощью Power Query

Одной из ключевых особенностей решения Майкрософт для работы с данными большого объема является полная интеграция компонентов бизнес-аналитики Майкрософт с кластерами Hadoop в HDInsight. Основным примером подобной интеграции является возможность подключения Excel к учетной записи хранения Azure, в которой хранятся данные, связанные с кластером Hadoop, с помощью Microsoft Power Query для Excel. В этой статье приводится пошаговое руководство по настройке и использованию Power Query в Excel для запроса данных, связанных с кластером Hadoop, который управляется с помощью HDInsight.

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

-   Кластер HDInsight. Для настройки кластера ознакомьтесь с разделом [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight].
-   Компьютер под управлением Windows 8, Windows 7, Windows Server 2012 или Windows Server 2008 R2.
-   Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

## Содержание

-   [Установка Microsoft Power Query для Excel][Установка Microsoft Power Query для Excel]
-   [Импорт данных в Excel][Импорт данных в Excel]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="InstallPowerQuery"></span></a>Установка Microsoft Power Query для Excel

Power Query можно использовать для импорта данных из различных источников в Microsoft Excel, где можно задействовать мощные средства бизнес-аналитики, например PowerPivot и Power View. В частности Power Query может импортировать данные, которые были выведены или созданы заданием Hadoop, работающим в кластере HDInsignt.

Загрузите Microsoft Power Query для Excel из [Центра загрузки Майкрософт][Центра загрузки Майкрософт] и установите.

## <span id="ImportData"></span></a>Импорт данных HDInsight в Excel

Надстройка Power Query для Excel удобна для импорта данных из кластера HDInsight в Excel, где можно использовать средства бизнес-аналитики, такие как PowerPivot и Power Map для изучения, анализа и презентации данных.

**Чтобы импортировать данные из кластера HDInsight, выполните следующие действия.**

1.  Откройте Excel.

2.  Создайте новую пустую книгу.

3.  Щелкните меню **Power Query**, выберите **Из других источников**, а затем щелкните **Из Windows Azure HDInsight**.

    ![HDI.PowerQuery.SelectHdiSource][HDI.PowerQuery.SelectHdiSource]

    Примечание. Если нет меню **Power Query**, последовательно выберите пункты **Файл** \> **Параметры** \> **Надстройки** и выберите **Надстройки COM** в раскрывающемся списке **Диспетчер** в нижней части страницы. Выберите кнопку **Перейти...** и убедитесь, что выделено поле надстройки Microsoft Office Power Query для Excel.

4.  Введите **Имя учетной записи** учетной записи хранилища BLOB-объектов Azure, связанной с вашим кластером, и нажмите кнопку **ОК**.

5.  Введите **Ключ учетной записи** для учетной записи хранилища BLOB-объектов Azure, а затем нажмите кнопку **Сохранить**. (Необходимо сделать это при первом доступе к хранилищу.)

6.  В области **Навигатор** слева от окна **Редактор запросов** дважды щелкните имя контейнера хранилища BLOB-объектов. По умолчанию имя контейнера совпадает с именем кластера.

7.  Найдите **HiveSampleData.txt** в столбце **Имя** (путь папки: **../hive/warehouse/hivesampletable/**), а затем щелкните **Двоичный код** в левой части HiveSampleData.txt.

    ![HDI.PowerQuery.ImportData][HDI.PowerQuery.ImportData]

8.  Если необходимо, можно переименовать имена столбцов. Когда будете готовы, щелкните **Применить и закрыть**.

    ![HDI.PowerQuery.ImportedTable][HDI.PowerQuery.ImportedTable]

## <span id="NextSteps"></span></a>Дальнейшие действия

В этой статье было показано, как использовать Power Query для получения данных из HDInsight в Excel. Аналогичным образом можно получить данные из HDInsight в SQL Azure. Можно также передавать данные в HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

-   [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC]
-   [Отправка данных в HDInsight][Отправка данных в HDInsight].

  [Приступая к работе с Azure HDInsight]: ../hdinsight-get-started/
  [Установка Microsoft Power Query для Excel]: #InstallPowerQuery
  [Импорт данных в Excel]: #ImportData
  [Дальнейшие действия]: #NextSteps
  [Центра загрузки Майкрософт]: http://go.microsoft.com/fwlink/?LinkID=286689
  [HDI.PowerQuery.SelectHdiSource]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
  [HDI.PowerQuery.ImportData]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
  [HDI.PowerQuery.ImportedTable]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG
  [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Отправка данных в HDInsight]: ../hdinsight-upload-data/
