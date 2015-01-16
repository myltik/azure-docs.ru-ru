<properties urlDisplayName="HDInsight and Excel" pageTitle="Подключение Excel к Hadoop с помощью Power Query | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="Узнайте, как эффективно использовать компоненты бизнес-аналитики и Excel для получения доступа к данным, хранящимся в Azure HDInsight, с помощью Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />




#Подключение Excel к Hadoop с помощью Power Query

Одной из ключевых особенностей решения Майкрософт для работы с данными большого объема является полная интеграция компонентов бизнес-аналитики Майкрософт с кластерами Hadoop в HDInsight. Основным примером подобной интеграции является возможность подключения Excel к учетной записи хранения Azure, в которой хранятся данные, связанные с кластером Hadoop, с помощью Microsoft Power Query для Excel. В этой статье приводится пошаговое руководство по настройке и использованию Power Query в Excel для запроса данных, связанных с кластером Hadoop, который управляется с помощью HDInsight. 

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

- Кластер HDInsight. Информацию о настройке кластера см. в статье [Приступая к работе с Azure HDInsight][hdinsight-get-started].
- Компьютер под управлением Windows 8, Windows 7, Windows Server 2012 или Windows Server 2008 R2.
- Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

## Содержание

- [Установка Microsoft Power Query для Excel](#InstallPowerQuery)
- [Импорт данных в Excel](#ImportData)
- [Дальнейшие действия](#NextSteps)


## <a id="InstallPowerQuery"></a>Установка Microsoft Power Query для Excel

Power Query можно использовать для импорта данных из различных источников в Microsoft Excel, где можно задействовать мощные средства бизнес-аналитики, например PowerPivot и Power View. В частности Power Query может импортировать данные, которые были выведены или созданы заданием Hadoop, работающим в кластере HDInsignt. 

Скачайте найдстройку Microsoft Power Query для Excel из [Центра загрузки Майкрософт][powerquery-download] и установите ее.

## <a id="ImportData"></a>Импорт данных HDInsight в Excel

Надстройка Power Query для Excel удобна для импорта данных из кластера HDInsight в Excel, где можно использовать средства бизнес-аналитики, такие как PowerPivot и Power Map для изучения, анализа и презентации данных.

**Импорт данных из кластера HDInsight**

1. Откройте Excel.

2. Создайте новую пустую книгу.

3. Щелкните меню **Power Query** и последовательно щелкните **Из других источников**, **Из Azure HDInsight**. 

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	Примечание. Если вы не видите меню **Power Query** , последовательно выберите **Файл** > **Параметры** > **Надстройки**, а затем в нижней части страницы в раскрывающемся списке **Диспетчер** выберите **Надстройки COM**. Нажмите кнопку **Перейти...** и убедитесь, что флажок надстройки Microsoft Office Power Query для Excel установлен.

3. В поле **Имя учетной записи** введите имя учетной записи хранилища больших двоичных объектов Azure, связанной с вашим кластером, и нажмите кнопку **ОК**. 

4. Введите значение в поле **Ключ учетной записи** для учетной записи хранения больших двоичных объектов, а затем нажмите кнопку **Сохранить**. (Необходимо сделать это при первом доступе к хранилищу.)	

5. В области **навигатора** в левой части окна **Редактор запросов** дважды щелкните имя контейнера хранилища больших двоичных объектов. По умолчанию имя контейнера совпадает с именем кластера. 

6. В столбце **Имя** найдите файл **HiveSampleData.txt** (путь к папке - **../hive/warehouse/hivesampletable/**) и в левой части HiveSampleData.txt щелкните **Бинарное**.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Если необходимо, можно переименовать имена столбцов. Когда будете готовы, щелкните **Применить и закрыть**.	

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>Дальнейшие действия

В этой статье было показано, как использовать Power Query для получения данных из HDInsight в Excel. Аналогичным образом можно получить данные из HDInsight в SQL Azure. Можно также передавать данные в HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-ODBC]
* [Отправка данных в HDInsight][hdinsight-upload-data].

[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png 
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG 

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689 

<!--HONumber=35.1-->
