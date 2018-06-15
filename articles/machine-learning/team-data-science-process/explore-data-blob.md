---
title: Просмотр данных в хранилище BLOB-объектов Azure с помощью Pandas | Документация Майкрософт
description: Описание просмотра данных, которые хранятся в контейнере больших двоичных объектов Azure, с помощью Pandas.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: feaa9e54-01e0-48c8-a917-1eba0f9d9ec7
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: c727df985f3285f5def9bfdc249ee27b4d748a01
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837054"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Просмотр данных в хранилище больших двоичных объектов Azure с помощью Pandas
В этом документе описывается просмотр данных, которые хранятся в контейнере больших двоичных объектов Azure, с помощью пакета Python [Pandas](http://pandas.pydata.org/) .

Следующее **меню** содержит ссылки на статьи, описывающие использование средств для просмотра данных из различных сред хранения. Эта задача является одним из этапов [процесса обработки и анализа данных]().

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>предварительным требованиям
В этой статье предполагается, что вы:

* Создали учетную запись хранения Azure. Инструкции см. в разделе [Создание учетной записи хранения](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Сохранили данные в учетной записи хранилища больших двоичных объектов Azure. Инструкции можно найти в статье [Перемещение данных в службу хранилища Azure и обратно](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Загрузка данных в кадр данных Pandas
Для просмотра набора данных и управления им набор необходимо сначала скачать из источника больших двоичных объектов в локальный файл, который в последствии можно загрузить в кадр данных Pandas. Ниже приведен порядок выполнения данной процедуры.

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

## <a name="blob-dataexploration"></a>Примеры просмотра данных с помощью Pandas
Вот несколько примеров того, как можно просматривать данные с помощью Pandas:

1. Проверьте **количество строк и столбцов** 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. **Проверьте** первые или последние несколько **строк** в следующем наборе данных:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Проверьте **тип данных** , импортированный в каждый столбец, с помощью приведенного ниже примера кода.
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Проверьте **основную статистику** , относящуюся к столбцам набора данных, так, как показано ниже.
   
        dataframe_blobdata.describe()
5. Проверьте количество записей в каждом столбце так, как показано ниже.
   
        dataframe_blobdata['<column_name>'].value_counts()
6. **Проверьте соотношение числа отсутствующих значений** к фактическому количеству записей в каждом столбце. Воспользуйтесь приведенным ниже примером кода.
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Если в том или ином столбце данных **отсутствуют значения** , вы можете заменить их так, как показано ниже.
   
     dataframe_blobdata_noNA = dataframe_blobdata.dropna()   dataframe_blobdata_noNA.shape
   
   Другой способ заменить отсутствующие значения — воспользоваться функцией режима.
   
     dataframe_blobdata_mode = dataframe_blobdata.fillna({'<имя_столбца>':dataframe_blobdata['<имя_столбца>'].mode()[0]})        
8. Создайте **гистограмму**, используя переменное количество ячеек, чтобы построить распределение переменной.    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Проверьте **корреляции** между переменными, используя диаграмму рассеяния или встроенную функцию корреляции.
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

