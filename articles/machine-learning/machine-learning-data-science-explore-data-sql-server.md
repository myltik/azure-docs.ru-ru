<properties 
	pageTitle="Просмотр данных в виртуальной машине SQL Server на платформе Azure | Microsoft Azure" 
	description="Описание того, как просматривать данные, хранящиеся в виртуальной машине SQL Server на платформе Azure." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="fashah;garye;bradsev" />

#Просмотр данных в виртуальной машине SQL Server на платформе Azure

Это **меню** содержит ссылки на разделы, описывающие использование средств для просмотра данных из различных сред хранения. Эта задача является одним из этапов процесса аналитики Кортаны (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

##Введение

В этом документе описано, как просматривать данные, хранящиеся в виртуальной машине SQL Server на платформе Azure. Это можно сделать путем структурирования данных с помощью SQL или с использованием языка программирования, например Python.


> [AZURE.NOTE]В образцах инструкций SQL, содержащихся в данном документе, предполагается, что данные находятся на сервере SQL Server. Если это не так, обратитесь к карте обработки облачных данных, чтобы узнать, как переместить данные в SQL Server.



## <a name="sql-dataexploration"></a>Просмотр данных SQL с помощью сценариев SQL

Вот несколько примеров сценариев SQL, которые можно использовать для изучения хранилищ данных в SQL Server.

1. Получение количества наблюдений за день

	`SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)`

2. Получение уровней в столбце категорий

	`select  distinct <column_name> from <databasename>`

3. Получение числа уровней в сочетании двух столбцов категорий

	`select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Получение распределения для числовых столбцов

	`select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE]Для практического примера можно использовать [Набор данных о такси Нью-Йорка](http://www.andresmh.com/nyctaxitrips/) и статью IPNB под названием [Структурирование данных Нью-Йорка с помощью IPython Notebook и SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb), содержащую полное пошаговое руководство.

##<a name="python"></a>Просмотр данных SQL с помощью Python

Использование языка Python для просмотра данных и создания характеристик, когда данные находятся в SQL Server, подобно обработке данных в большом двоичном объекте Azure с использованием Python, как описано в разделе [Обработка данных большого двоичного объекта Azure в среде обработки данных](machine-learning-data-science-process-data-blob.md). Данные необходимо загрузить из базы данных во фрейм данных pandas для последующей обработки. В этом разделе документирован процесс подключения к базе данных и загрузки данных во фрейм данных.

Для подключения к базе данных SQL Server из языка Python с использованием pyodbc можно применить следующий формат строки подключения (замените servername, dbname, username и password соответствующими значениями имени сервера, имени БД, имени пользователя и пароля):

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Библиотека Pandas](http://pandas.pydata.org/) в языке Python предлагает большой выбор структур данных и средств анализа данных для манипуляций со значениями с помощью языке Python. Приведенный ниже код считывает результаты, возвращенные из базы данных SQL Server, во фрейм данных Pandas:

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Теперь можно работать с фреймом данных Pandas, как описано в статье [Обработка данных большого двоичного объекта Azure в среде обработки данных](machine-learning-data-science-process-data-blob.md).

## Практический пример применения процесса аналитики Кортаны

Полноценный пошаговый пример применения процесса аналитики Кортаны с использованием общедоступного набора данных см. в разделе [Процесс аналитики Кортаны в действии: использование SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

 

<!---HONumber=Oct15_HO4-->