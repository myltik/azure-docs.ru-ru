---
title: Обработка больших двоичных данных Azure с применением методов расширенного анализа | Документация Майкрософт
description: Обработка данных в хранилище больших двоичных объектов Azure.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d8a59078-91d3-4440-b85c-430363c3f4d1
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 3daf86f59a84f8c442581160142dcf806173b626
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836608"
---
# <a name="heading"></a>Обработка больших двоичных данных Azure с применением методов расширенного анализа
Этот документ содержит сведения о работе с данными в хранилище больших двоичных объектов Azure и создании характеристик на их основе. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Загрузка данных во фрейм данных Pandas
Для просмотра набора данных и управления им набор необходимо скачать из источника больших двоичных объектов в локальный файл, который затем можно загрузить в кадр данных Pandas. Ниже приведен порядок выполнения данной процедуры.

1. Скачайте данные из большого двоичного объекта Azure с помощью службы BLOB-объектов. Для этого воспользуйтесь приведенным ниже примером кода Python. Замените переменные этого кода своими значениями. 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. Прочитайте данные, которые содержит скачанный файл, в блоке данных Pandas.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Теперь вы готовы просматривать эти данные и создавать функции на основе этого набора данных.

## <a name="blob-dataexploration"></a>Просмотр данных
Вот несколько примеров того, как можно просматривать данные с помощью Pandas:

1. Проверьте количество строк и столбцов. 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Проверьте первые или последние несколько строк в наборе данных так, как показано ниже.
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Проверьте тип данных, импортированный в каждый столбец, с помощью приведенного ниже образца кода.
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Проверьте основную статистику, относящуюся к столбцам набора данных, так, как показано ниже.
   
        dataframe_blobdata.describe()
5. Проверьте количество записей в каждом столбце так, как показано ниже.
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Проверьте соотношение числа отсутствующих значений к фактическому количеству записей в каждом столбце. Воспользуйтесь приведенным ниже образцом кода.
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Если в том или ином столбце данных отсутствуют значения, вы можете заменить их так, как показано ниже.
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna()   dataframe_blobdata_noNA.shape
   
   Другой способ заменить отсутствующие значения — воспользоваться функцией режима.
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Создайте гистограмму, используя переменное количество ячеек, чтобы построить распределение переменной.    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Проверьте корреляции между переменными, используя диаграмму рассеяния или встроенную функцию корреляции.
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Создание характеристик
Создавать функции с помощью Python вы можете приведенным ниже способом.

### <a name="blob-countfeature"></a>Создание функций на основе значений индикатора
Вот как можно создавать категориальные функции:

1. Проверьте распределение категориального столбца.
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Создайте значения индикатора для каждого значения столбца.
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Объедините столбец индикатора с исходным блоком данных. 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Удалите исходную переменную.
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Создание характеристик путем группирования данных
Вот как можно создавать функции группирования:

1. Добавьте последовательность столбцов, чтобы создать числовой столбец.
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Преобразуйте группирование в последовательность логических переменных.
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Наконец, объедините фиктивные переменные с исходным блоком данных.
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Запись данных обратно в большой двоичный объект Azure и их использование в Студии машинного обучения Azure
После просмотра данных и создания необходимых вам признаков вы можете отправить данные (в выборке или в признаке) в большой двоичный объект Azure и использовать их в Студии машинного обучения Azure. Вы можете это сделать описанным ниже способом. Обратите внимание на то, что дополнительные характеристики можно создавать и в Студии машинного обучения Microsoft Azure. 

1. Запишите блок данных в локальный файл.
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Отправьте данные в большой двоичный объект Azure так, как указано ниже.
   
        from azure.storage.blob import BlobService
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
3. Теперь данные можно считывать из большого двоичного объекта с помощью модуля [Импорт данных][import-data] машинного обучения Azure (см. рисунок ниже).

![большой двоичный объект считывателя][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

