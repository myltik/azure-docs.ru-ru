---
title: Выборка данных в хранилище BLOB-объектов Azure | Документация Майкрософт
description: Выборка данных в хранилище больших двоичных объектов Azure
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: e8d9ad2c-86c5-43d6-80b8-d355b5c0dccf
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: ffb92df0e0727d02985ec26f61739cdc759d0f93
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837673"
---
# <a name="heading"></a>Выборка данных в хранилище больших двоичных объектов Azure
Этот документ описывает выборку данных, содержащихся в хранилище BLOB-объектов Azure, путем их программной загрузки и последующей выборки с использованием процедур на языке Python.

**Меню** ниже содержит ссылки на разделы, в которых описана выборка данных из различных сред хранения. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Для чего нужна выборка данных?**
Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Это способствует пониманию данных, их исследованию и проектированию характеристик. Роль этой операции в процессе аналитики Кортаны заключается в том, чтобы сделать возможным быстрое прототипирование функций обработки данных и моделей машинного обучения.

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="download-and-down-sample-data"></a>Скачать данные и уменьшить выборку данных
1. Загрузить данные из хранилища BLOB-объектов Azure с помощью службы BLOB-объектов и следующего образца кода на языке Python: 
   
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

2. Считать данные во фрейм данных Pandas из файла, загруженного выше.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Уменьшить выборку данных с помощью `random.choice` `numpy`:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Теперь можно работать с указанным выше фреймом данных, содержащим 1-процентную выборку, для дальнейшего просмотра и создания характеристик.

## <a name="heading"></a>Передача данных и их считывание в службу машинного обучения Azure
С помощью следующего образца кода можно уменьшить выборку данных и использовать их непосредственно в Машинном обучении Azure.

1. Запись фрейма данных в локальный файл
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Передача локального файла в BLOB-объект Azure с помощью следующего образца кода:
   
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
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Считывание данных из большого двоичного объекта Azure с помощью модуля [Импорт данных](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) Машинного обучения Azure, как показано на рисунке ниже.

![большой двоичный объект считывателя](./media/sample-data-blob/reader_blob.png)

