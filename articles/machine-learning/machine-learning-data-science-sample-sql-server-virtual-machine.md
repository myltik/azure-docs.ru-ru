<properties 
	pageTitle="Выборка данных на сервере SQL Server в Azure| Azure" 
	description="Выборка данных на сервере SQL Server в Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="fashah,garye" /> 

#<a name="heading"></a>Выборка данных на сервере SQL Server в Azure

В этом документе описывается выборка данных, хранящихся на сервере SQL Server в системе Azure, одним из следующих способов:

1. [С помощью SQL](#sql)
2. [С помощью языка программирования Python](#python) 

**Примечание**
>Образец кода SQL в этом документе предполагает, что данные содержатся на сервере SQL Server на платформе Azure. Если этот не так, обратитесь к карте процесса обработки облачных данных для получения инструкций по перемещению данных на сервер SQL Server в системе Azure.

###<a name="SQL"></a>С помощью SQL

В этом разделе описываются несколько методов использования SQL для выполнения простой случайной выборки из данных, содержащихся в базе данных. Выберите нужный метод в зависимости от размера ваших данных и их распределения.

Два элемента ниже показывают, как использовать newid в SQL Server для выполнения выборки. Выбор метода зависит от того, насколько случайной требуется сделать выборку (pk_id в образце кода ниже предполагается автоматически генерируемым первичным ключом).

1. Менее строгая случайная выборка

	    select  * from <table_name> where <primary_key> in 
    	(select top 10 percent <primary_key> from <table_name> order by newid())

2. Более случайная выборка 

	    SELECT * FROM <table_name>
    	WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Для выборки можно также использовать предложение TABLESAMPLE, как показано ниже. Этот подход рекомендуется использовать для работы с данными большого размера (предполагается, что данные на разных страницах не коррелируют между собой) и для выполнения запроса в разумные сроки.

	SELECT *
	FROM <table_name> 
	TABLESAMPLE (10 PERCENT)

**Примечание**
> Можно просматривать данные этой выборки и создавать характеристики, сохранив ее в новой таблице


####<a name="sql-aml"></a>Подключение к службе машинного обучения Azure

Приведенные выше примеры запросов можно использовать непосредственно в модуле Azure ML Reader для уменьшения выборки данных на лету и подачи их в эксперимент Azure ML. Ниже показан снимок экрана при использовании модуля Reader для считывания данных выборки:
   
![reader sql][1]

###<a name="python"></a>С помощью языка программирования Python 

В этом разделе демонстрируется использование библиотеки pyodbc для подключения к базе данных SQL Server на языке Python. Строка подключения к базе данных выглядит следующим образом: (замените servername, dbname, username и password соответственно именем сервера, именем базы данных, именем пользователя и паролем из вашей конфигурации):

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Библиотека [Pandas](http://pandas.pydata.org/) в языке Python предоставляет широкий набор структур данных и средств анализа данных для манипуляций с данными при программировании на языке Python. Приведенный ниже код считывает 0,1%-ную выборку данных из таблицы базы данных Azure SQL в данные Pandas:

	import pandas as pd

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Теперь можно работать с данными выборки во фрейме данных Pandas. 

####<a name="python-aml"></a>Подключение к службе машинного обучения Azure

С помощью следующего образца кода можно сохранить данные уменьшенной выборки в файл и передать его в BLOB-объект Azure. Данные в BLOB-объекте можно непосредственно считать в эксперимент Azure ML с помощью модуля *Reader Module*. Для этого необходимо выполнить следующие шаги: 

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

3. Считывание данных из BLOB-объекта Azure с помощью модуля Azure ML *Reader Module*, как показано на снимке экрана ниже:
 
![reader blob][2]

### Пример применения обработки данных в Azure на практике

Пример комплексного пошагового руководства по обработке данных в Azure с использованием общедоступного набора данных см. в разделе [Обработка данных в Azure на практике](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png


<!--HONumber=49--> 