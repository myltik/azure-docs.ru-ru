---
title: Создание признаков для данных хранилища BLOB-объектов Azure с помощью Panda | Документация Майкрософт
description: Описание создания характеристик для данных, которые хранятся в контейнере больших двоичных объектов Azure, с помощью пакета Python Pandas.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 676b5fb0-4c89-4516-b3a8-e78ae3ca078d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 99e54400b070a03744c4d4504fa4c89ad6dc097d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837452"
---
# <a name="create-features-for-azure-blob-storage-data-using-panda"></a>Создание характеристик для данных хранилища больших двоичных объектов Azure с помощью Panda
В этом документе демонстрируется создание характеристик для данных, которые хранятся в контейнере больших двоичных объектов Azure, с помощью пакета Python [Pandas](http://pandas.pydata.org/) . После описания загрузки данных в кадр данных Panda в нем показано создание категориальных характеристик с использованием сценариев Pyrhon со значениями индикатора и характеристиками группирования.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Это **меню** содержит ссылки на статьи, описывающие создание характеристик для данных в различных средах. Эта задача является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>предварительным требованиям
В этой статье предполагается, что вы уже создали учетную запись хранилища BLOB-объектов Azure и сохранили в ней свои данные. Инструкции по настройке учетной записи в Azure см. в разделе [Создание учетной записи хранения](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

## <a name="load-the-data-into-a-pandas-data-frame"></a>Загрузка данных во фрейм данных Pandas
Чтобы просматривать набор данных и управлять им, скачайте его из исходного BLOB-объекта в локальный файл. Затем загрузите его во фрейм данных Pandas. Ниже приведен порядок выполнения данной процедуры.

1. Скачайте данные из большого двоичного объекта Azure с помощью службы BLOB-объектов. Для этого воспользуйтесь приведенным ниже примером кода Python. Замените переменные в этом коде своими значениями.
   
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

## <a name="blob-featuregen"></a>Создание функций
В двух следующих разделах показано, как создать категориальные характеристики со значениями индикатора и характеристики группирования с помощью сценариев Python.

### <a name="blob-countfeature"></a>Создание признаков со значениями индикатора
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
Вот как можно создать сгруппированные признаки:

1. Добавьте последовательность столбцов, чтобы создать числовой столбец.
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Преобразуйте группирование в последовательность логических переменных.
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Наконец, объедините фиктивные переменные с исходным блоком данных.
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

## <a name="sql-featuregen"></a>Запись данных обратно в BLOB-объект Azure и их использование в службе "Машинное обучение Azure"
Если вы изучили данные, сделали из них выборку или создали для них признаки и теперь хотите использовать эти данные в службе "Машинное обучение Azure", передайте эти данные в BLOB-объект Azure. Дополнительные признаки можно создать также в Студии машинного обучения Azure. Чтобы передать данные в облако, сделайте следующее:

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
3. Теперь данные можно считывать из BLOB-объекта с помощью модуля [Импорт данных](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) службы "Машинное обучение Azure" (см. рисунок ниже).

![большой двоичный объект считывателя](./media/data-blob/reader_blob.png)

