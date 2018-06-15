---
title: Выборка данных на сервере SQL Server в Azure | Документация Майкрософт
description: Выборка данных на сервере SQL Server в Azure
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgeonlun
editor: cgronlun
ms.assetid: 33c030d4-5cca-4cc9-99d7-2bd13a3926af
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 74dcef9e927fc537cba56b03fcbfb9528c952ad0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837877"
---
# <a name="heading"></a>Выборка данных на сервере SQL Server в Azure
В этой статье описывается процесс выборки данных, хранящихся на сервере SQL Server в Azure, с помощью SQL или языка программирования Python. В нем также показано, как переместить данные выборки в службу машинного обучения Azure, сохранив их в файл, передав его в BLOB-объект Azure, а затем считав в студии машинного обучения Azure.

Процедура выборки Python использует библиотеку [pyodbc](https://code.google.com/p/pyodbc/) ODBC для подключения к SQL Server в Azure и библиотеку [Pandas](http://pandas.pydata.org/) для выполнения выборки.

> [!NOTE]
> Образец кода SQL в этом документе предполагает, что данные содержатся на сервере SQL Server на платформе Azure. Если это не так, воспользуйтесь инструкциями по переносу данных на сервер SQL Server в среде Azure, изложенными в статье [Перемещение данных в SQL Server на виртуальной машине Azure](move-sql-server-virtual-machine.md).
> 
> 

**Меню** ниже содержит ссылки на статьи, в которых описана выборка данных из различных сред хранения. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Для чего нужна выборка данных?**
Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Это способствует пониманию данных, их исследованию и проектированию характеристик. Роль этой операции в [процессе обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) состоит в том, чтобы сделать возможным быстрое прототипирование функций обработки данных и моделей машинного обучения.

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="SQL"></a>Использование SQL
В этом разделе описываются несколько методов использования SQL для выполнения простой случайной выборки из данных, содержащихся в базе данных. Выберите нужный метод в зависимости от размера ваших данных и их распределения.

Следующие два элемента показывают, как использовать `newid` в SQL Server для выполнения выборки. Выбор метода зависит от того, насколько случайной требуется сделать выборку (pk_id в образце кода считается автоматически генерируемым первичным ключом).

1. Менее строгая случайная выборка
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Более случайная выборка 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Для выборки данных можно также использовать предложение TABLESAMPLE. Этот подход рекомендуется использовать для работы с данными большого размера (предполагается, что данные на разных страницах не коррелируют между собой) и для выполнения запроса в разумные сроки.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Можно просматривать данные этой выборки и создавать характеристики, сохранив ее в новой таблице
> 
> 

### <a name="sql-aml"></a>Подключение к службе машинного обучения Azure
Приведенные выше примеры запросов можно использовать непосредственно в модуле [Импорт данных][import-data] Машинного обучения Azure для оперативного сокращения выборки данных и их передачи в эксперимент машинного обучения Azure. Ниже показан снимок экрана при использовании модуля Reader для считывания данных выборки.

![считыватель sql][1]

## <a name="python"></a>Использование языка программирования Python
В этом разделе демонстрируется использование [библиотеки pyodbc](https://code.google.com/p/pyodbc/) для установки подключения ODBC к Базе данных SQL Server на языке Python. Строка подключения к базе данных выглядит следующим образом (замените servername, dbname, username и password соответственно именем сервера, именем базы данных, именем пользователя и паролем из вашей конфигурации):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Библиотека [Pandas](http://pandas.pydata.org/) в языке Python предоставляет широкий набор структур данных и средств анализа данных для манипуляций с данными при программировании на языке Python. Следующий код считывает 0,1%-ную выборку данных из таблицы базы данных SQL Azure в данные Pandas.

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Теперь можно работать с данными выборки во фрейме данных Pandas. 

### <a name="python-aml"></a>Подключение к службе машинного обучения Azure
С помощью следующего образца кода можно сохранить данные уменьшенной выборки в файл и передать его в BLOB-объект Azure. Данные из большого двоичного объекта можно считать непосредственно в эксперимент машинного обучения Azure с помощью модуля [Импорт данных][import-data]. Для этого необходимо выполнить следующие шаги: 

1. Запись фрейма данных pandas в локальный файл
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Передача локального файла в BLOB-объект Azure
   
        from azure.storage import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Считать данные из BLOB-объекта Azure с помощью модуля [Импорт данных][import-data] службы Машинного обучения Azure, как показано на снимке экрана ниже.

![большой двоичный объект считывателя][2]

## <a name="the-team-data-science-process-in-action-example"></a>Пример применения процесса обработки и анализа данных группы на практике
Пошаговый пример применения командного процесса обработки и анализа данных с использованием общедоступного набора данных см. в статье [Процесс обработки и анализа данных группы на практике: использование SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
