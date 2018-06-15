---
title: Создание признаков для данных в SQL Server с помощью SQL и Python | Документация Майкрософт
description: Обработка данных из SQL Azure
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: ''
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 432f7b9bb27c1dee396677c54edf48d9fdb027a0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836340"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Создание характеристик для данных в SQL Server с помощью SQL и Python
В этом документе показано, как создавать характеристики для данных, которые хранятся в виртуальной машине SQL Server в Azure, и помогают алгоритмам эффективнее обучаться. Для выполнения этой задачи можно использовать SQL или язык программирования, например Python. В этой статье описаны оба подхода.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Это **меню** содержит ссылки на статьи, описывающие создание характеристик для данных в различных средах. Эта задача является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [!NOTE]
> Для практического примера можно использовать [набор данных о такси Нью-Йорка](http://www.andresmh.com/nyctaxitrips/) и статью IPNB под названием [Структурирование данных Нью-Йорка с помощью IPython Notebook и SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb), содержащую полное пошаговое руководство.
> 
> 

## <a name="prerequisites"></a>предварительным требованиям
В этой статье предполагается, что вы:

* Создали учетную запись хранения Azure. Инструкции см. в разделе [Создание учетной записи хранения](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Сохранили данные в SQL Server. Если вы еще не сделали это, см. статью [Перемещение данных в базу данных SQL Azure для машинного обучения Azure](move-sql-azure.md). Она содержит инструкции по перемещению данных.

## <a name="sql-featuregen"></a>Создание признаков с помощью SQL
В этом разделе мы опишем способы создания характеристик с помощью SQL:  

1. [Создание характеристик на основе количества](#sql-countfeature)
2. [Создание характеристик путем группирования данных](#sql-binningfeature)
3. [Развертывание характеристик из одного столбца](#sql-featurerollout)

> [!NOTE]
> После создания дополнительных компонентов можно либо добавить их в виде столбцов в существующую таблицу, либо создать новую таблицу с дополнительными компонентами и первичным ключом, которую можно будет объединить с исходной таблицей.
> 
> 

### <a name="sql-countfeature"></a>Создание признаков на основе количества
В этом документе показаны два способа создания количественных характеристик. В первом способе используется условная сумма, а во втором — предложение "where". Затем их можно объединить с исходной таблицей (с помощью столбцов первичных ключей), чтобы включить количественные характеристики вместе с исходными данными.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Создание характеристик путем группирования данных
В следующем примере показано, как создать группированные характеристики путем группирования (с использованием 5 ячеек) числового столбца, который взамен можно использовать как характеристику:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Развертывание характеристик из одного столбца
В этом разделе мы покажем, как развернуть одиночный столбец в таблице для создания дополнительных характеристик. В примере предполагается, что в таблице, из которой вы намерены создать характеристики, содержится столбец широты или долготы.

Вот краткое руководство по данным широты/долготы расположения (на основе ресурса stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Ниже приведены некоторые важные сведения о расположении данных, которые нужно учитывать, прежде чем создавать признаки из поля:

* Знак указывает, в каком полушарии мы находимся: северном или южном, восточном или западном.
* Ненулевая цифра сотен указывает, что используется долгота, а не широта.
* Цифра десятков сообщает положение с точностью примерно 1000 километров. Она указывает, на каком континенте или возле какого океана мы находимся.
* Цифра единиц (один десятичный градус) дает положение с точностью до 111 километров (60 морских миль, или около 69 миль). Она приблизительно указывает, в каком крупном регионе или стране мы находимся.
* Первый десятичный разряд соответствует точности до 11,1 км: он позволяет различать положение одного крупного города относительно соседнего крупного города.
* Второй десятичный разряд соответствует точности до 1,1 км: он позволяет различать мелкие населенные пункты.
* Третий десятичный разряд соответствует точности до 110 м: он позволяет идентифицировать крупное сельскохозяйственное поле или кампус учреждения.
* Четвертый десятичный разряд соответствует точности до 11 м: он позволяет идентифицировать участок земли. Это сравнимо с типичной точностью устройства GPS без коррекции при отсутствии помех.
* Пятый десятичный разряд соответствует точности до 1,1 м: он позволяет отличать деревья друг от друга. Точность этого уровня с помощью коммерческих GPS-устройств может быть достигнута только при использовании дифференциальной коррекции.
* Шестой десятичный разряд соответствует точности до 0,11 м: это значение можно использовать для подробной детализации сооружений, для проектирования ландшафтов, строительства дорог. Этого уровня более чем достаточно для отслеживания движения ледников и рек. Его можно достичь с помощью тщательного измерения с использованием GPS, например при дифференциальной коррекции GPS.

Сведения о расположении можно включить в признаки, выделяя информацию о регионе, расположении и городе. Обратите внимание на то, что для получения сведений об области или районе можно также вызвать конечную точку REST, например интерфейс Bing Maps API, доступный по адресу `https://msdn.microsoft.com/library/ff701710.aspx` .

    select
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Признаки на основе расположения можно в дальнейшем использовать для создания дополнительных количественных признаков, как было описано ранее.

> [!TIP]
> Можно программным путем вставлять записи с использованием выбранного языка. Чтобы оптимизировать запись, данные можно вставлять блоками. Пример такого решения с использованием pyodbc см. [по этой ссылке](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Другой вариант — вставить данные в базу данных с использованием [служебной программы BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Подключение к службе машинного обучения Azure
Новую созданную характеристику можно добавить в виде столбца в существующую таблицу или сохранить в новой таблице и объединить с существующей таблицей для машинного обучения. Вы можете создать признаки и открыть доступ к ним, если они уже созданы, с помощью модуля [Импорт данных](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) в Машинном обучении Azure, как показано ниже.

![Модули чтения в службе "Машинное обучение Azure"](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Использование языка программирования, например Python
Использование языка Python для создания признаков, когда данные находятся в SQL Server, аналогично обработке данных в большом двоичном объекте Azure с помощью Python. Сравнение см. в статье [Обработка больших двоичных данных Azure с применением методов расширенного анализа](data-blob.md). Загрузите данные из базы данных во фрейм данных Pandas для последующей обработки. В этом разделе описано, как подключиться к базе данных и загрузить данные во фрейм данных.

Для подключения к базе данных SQL Server из языка Python с использованием pyodbc можно применить следующий формат строки подключения (замените servername, dbname, username и password соответствующими значениями имени сервера, имени БД, имени пользователя и пароля):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Библиотека Pandas](http://pandas.pydata.org/) в языке Python предлагает большой выбор структур данных и средств анализа данных для манипуляций со значениями с помощью языке Python. Следующий код считывает результаты, возвращенные из базы данных SQL Server, в кадр данных Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Теперь можно работать с кадром данных Pandas, как описано в статье [Создание характеристик для данных хранилища больших двоичных объектов Azure с помощью Panda](create-features-blob.md).

