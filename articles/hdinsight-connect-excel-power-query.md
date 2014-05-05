<properties linkid="manage-services-hdinsight-connect-excel-with-power-query" urlDisplayName="HDInsight и Excel" pageTitle="Подключение Excel к HDInsight с помощью Power Query | Azure" metaKeywords="hdinsight, excel, обозреватель данных, hive excel, hdinsight excel, power query" description="Использование компонентов бизнес-аналитики и Excel для получения данных, хранящихся в Azure HDInsight с помощью Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Подключение Excel к Azure HDInsight с помощью Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />




#Подключение Excel к HDInsight с помощью Power Query

Одной из ключевых особенностей решения Майкрософт для работы с данными большого объема является полная интеграция компонентов бизнес-аналитики Майкрософт с кластерами HDInsight Hadoop. Основным примером подобной интеграции является возможность подключения Excel к учетной записи хранения Azure, в которой хранятся данные, связанные с кластером HDInsight, с помощью Microsoft Power Query для Excel. В этой статье приводится пошаговое руководство по настройке и использованию Power Query в Excel для запроса данных, связанных с кластером HDInsight. 

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

- Кластер HDInsight. Для настройки кластера ознакомьтесь с разделом [Приступая к работе с Azure HDInsight][hdinsight-get-started].
- Компьютер под управлением Windows 8, Windows 7, Windows Server 2012 или Windows Server 2008 R2.
- Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

##В этой статье

- [Установка Microsoft Power Query для Excel](#InstallPowerQuery)
- [Импорт данных в Excel](#ImportData)
- [Дальнейшие действия](#NextSteps)


## <a id="InstallPowerQuery"></a>Установка Microsoft Power Query для Excel

Power Query можно использовать для импорта данных из различных источников в Microsoft Excel, где можно задействовать мощные средства бизнес-аналитики, например PowerPivot и Power View. В частности Power Query может импортировать данные, которые были выведены или созданы заданием Hadoop, работающим в кластере HDInsignt. 

Загрузите Microsoft Power Query для Excel из [Центра загрузки Майкрософт][powerquery-download] и установите.

## <a id="ImportData"></a>Импортируйте данные HDInsight в Excel

Надстройка Power Query для Excel удобна для импорта данных из кластера HDInsight в Excel, где можно использовать средства бизнес-аналитики, такие как PowerPivot и Power Map для изучения, анализа и презентации данных.

**Чтобы импортировать данные из кластера HDInsight, выполните следующие действия.**

1. Откройте Excel.

2. Создайте новую пустую книгу.

3. Щелкните меню **Power Query**, щелкните **Из других источников**, затем щелкните **Из Azure HDInsight**. 

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	Примечание. Если нет меню **Power Query** последовательно выберите пункты **Файл** > **Параметры** > **Надстройки** и выберите **Надстройки COM** в раскрывающемся списке **Диспетчер** в нижней части страницы. Выберите кнопку **Перейти...** и убедитесь, что выделено поле надстройки Microsoft Office Power Query для Excel.

3. Введите **Имя учетной записи** учетной записи хранилища BLOB-объектов Azure, связанной с вашим кластером, и нажмите кнопку **ОК**. 

4. Введите **Ключ учетной записи** для учетной записи хранения BLOB-объектов, а затем нажмите кнопку **Сохранить**. (Необходимо сделать это при первом доступе к хранилищу.)	

5. В области **навигатора** в правой части окна **Редактор очереди** дважды щелкните имя контейнера хранилища BLOB-объектов. По умолчанию имя контейнера совпадает с именем кластера. 

6. Найдите **HiveSampleData.txt** в столбце **Имя** (путь к папке **../hive/warehouse/hivesampletable/**) и щелкните **Двоичный код** в левой части HiveSampleData.txt.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Если необходимо, можно переименовать имена столбцов. Когда будете готовы, щелкните **Применить и закрыть**.	

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>Дальнейшие действия

В этой статье было показано, как использовать Power Query для получения данных из HDInsight в Excel. Аналогичным образом можно получить данные из HDInsight в SQL Azure. Можно также передавать данные в HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-excel-odbc]
* [Передача данных в HDInsight][hdinsight-upload-data].

[hdinsight-excel-odbc]: /ru-ru/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-get-started]: /ru-ru/documentation/articles/hdinsight-get-started/
[hdinsight-upload-data]: /ru-ru/documentation/articles/hdinsight-upload-data/

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png 
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG 

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689 

