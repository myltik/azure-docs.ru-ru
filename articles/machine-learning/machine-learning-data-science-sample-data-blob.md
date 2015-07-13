<properties 
	pageTitle="Выборка данных в хранилище больших двоичных объектов Azure| Microsoft Azure" 
	description="Выборка данных в хранилище BLOB-объектов Azure" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="sunliangms;fashah;msolhab;garye;bradsev" />

#<a name="heading"></a>Выборка данных в хранилище больших двоичных объектов Azure

Этот документ описывает выборку данных, содержащихся в хранилище BLOB-объектов Azure, путем их программной загрузки и последующей выборки с помощью образца кода на языке Python. Для этого необходимо выполнить следующие шаги:

1. Загрузить данные из хранилища BLOB-объектов Azure с помощью службы BLOB-объектов и следующего образца кода на языке Python: 

	    from azure.storage import BlobService
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

3. Создать выборку данных с помощью `random.choice` `numpy`:

	    # A 1 percent sample
    	sample_ratio = 0.01 
    	sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    	sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    	dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

	Теперь можно работать с указанным выше фреймом данных, содержащим 1-процентную выборку, для дальнейшего просмотра и создания характеристик.

##<a name="heading"></a>Подключение к службе машинного обучения Azure

С помощью следующего образца кода можно уменьшить выборку данных и использовать их непосредственно в Azure ML:

1. Запись фрейма данных в локальный файл

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Передача локального файла в BLOB-объект Azure с помощью следующего образца кода:

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
		    print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Считывание данных из большого двоичного объекта Azure с помощью модуля [считывателя](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) в Azure ML, как показано на изображении ниже:
 
![большой двоичный объект считывателя][1]

[1]: ./media/machine-learning-data-science-sample-data-blob/reader_blob.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=July15_HO1-->