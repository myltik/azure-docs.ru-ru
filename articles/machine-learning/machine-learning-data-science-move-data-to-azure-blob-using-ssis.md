<properties
	pageTitle="Перемещение данных в хранилище BLOB-объектов Azure и из него с помощью соединителей SSIS | Microsoft Azure"
	description="Перемещение данных в хранилище BLOB-объектов Azure и из него с помощью соединителей SSIS."
	services="machine-learning,storage"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="bradsev" />

# Перемещение данных в хранилище BLOB-объектов Azure и из него с помощью соединителей служб SSIS

[Пакет дополнительных компонентов SQL Server Integration Services для Azure](https://msdn.microsoft.com/library/mt146770.aspx) содержит компоненты для подключения к Azure, передачи данных между Azure и локальными источниками данных, а также для обработки данных, хранящихся в Azure.

Сведения о технологиях, которые используются для перемещения данных в хранилище больших двоичных объектов Azure и из него, см. по следующим ссылкам:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


После перемещения в облако локальные данные становятся доступными из любых служб Azure. Теперь можно использовать все возможности набора технологий Azure. К примеру, их можно использовать в машинном обучении Azure или в кластере HDInsight.

Обычно это является первым шагом для работы с [SQL](machine-learning-data-science-process-sql-walkthrough.md) и [HDInsight](machine-learning-data-science-process-hive-walkthrough.md).

В Интернете доступна информация о канонических сценариях, в рамках которых службы SSIS используются для выполнения бизнес-задач, часто встречающихся в сценариях интеграции гибридных данных. Найти ее можно в блоге [Повышенная эффективность при использовании пакета дополнительных компонентов SQL Server Integration Services для Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx).

> [AZURE.NOTE] Полное описание базовых принципов использования хранилища BLOB-объектов Azure см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Предварительные требования

Для выполнения описанных в этой статье задач необходимо иметь подписку Azure и учетную запись хранения Azure. Чтобы отправлять и скачивать данные, необходимо знать имя учетной записи хранения Azure и ее ключ.

- Сведения о настройке **подписки Azure** см. на странице [Бесплатная пробная версия на один месяц](https://azure.microsoft.com/pricing/free-trial/).

- Указания по созданию **учетной записи хранения** и получению сведений об учетной записи и ключах см. в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md).


Чтобы использовать **Соединители SSIS**, необходимо загрузить следующие компоненты:

- **SQL Server 2014 или 2016 Standard (или более поздняя версия)**: установка включает в себя SQL Server Integration Services.
- **Пакет дополнительных компонентов Microsoft SQL Server 2014 или 2016 Integration Services для Azure**: пакет можно скачать на странице [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) или [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) соответственно.

> [AZURE.NOTE] Службы SSIS устанавливаются вместе с SQL Server, но в версию Express они не включены. Сведения о приложениях, которые включены в различные выпуски SQL Server, см. в статье [Выпуски SQL Server](http://www.microsoft.com/ru-RU/server-cloud/products/sql-server-editions/).

Обучающие материалы о службах SSIS см. в статье [Предварительное обучение по службам SSIS](http://www.microsoft.com/download/details.aspx?id=20766).

Дополнительные сведения о том, как с помощью SISS получить рабочую среду для создания простых пакетов извлечения, преобразования и загрузки (ETL), см. в статье [Руководство по службам SSIS: создание простого пакета извлечения, преобразования и загрузки](https://msdn.microsoft.com/library/ms169917.aspx).

## Загрузка набора данных для такси Нью-Йорка  
В приведенном здесь примере используется общедоступный набор данных [Поездки в такси по Нью-Йорку](http://www.andresmh.com/nyctaxitrips/). Набор данных состоит из информации об около 173 миллионах поездок в такси в Нью-Йорке в 2013 году. В нем используется два типа данных: сведения о поездках и тарифах. Так как для каждого месяца заведен отдельный файл, всего у нас есть 24 файла, каждый из которых занимает объем примерно 2 ГБ (в несжатом виде).


## Отправка данных в хранилище больших двоичных объектов Azure
Чтобы переместить данные с помощью пакета дополнительных компонентов SSIS из локальной среды в хранилище BLOB-объектов Azure, мы используем экземпляр [**Задачи передачи BLOB-объектов Azure**](https://msdn.microsoft.com/library/mt146776.aspx), как показано здесь:

![configure-data-science-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


Ниже описаны параметры, используемые задачей.


Поле|Описание|
----------------------|----------------|
**AzureStorageConnection**|Задает существующий диспетчер подключения к хранилищу Azure или создает новый, который ссылается на учетную запись хранения Azure, указывающую на размещение файлов BLOB-объектов.|
**BlobContainer**|Задает имя контейнера больших двоичных объектов, который содержит отправленные файлы в виде больших двоичных объектов.|
**BlobDirectory**|Задает каталог больших двоичных объектов, в котором отправленный файл хранится в виде блочного BLOB-объекта. Каталог BLOB-объектов имеет виртуальную иерархическую структуру. Если большой двоичный объект уже существует, он заменяется.|
**LocalDirectory**|Задает локальный каталог с файлами для отправки.|
**FileName**|Указывает имя фильтра для выбора файлов с указанным шаблоном имени. Например, MySheet*.xls * включает в себя файлы MySheet001.xls и MySheetABC.xlsx.|
**TimeRangeFrom/TimeRangeTo**|Задает фильтр диапазона времени. Сюда включаются файлы, входящие в диапазон, крайними значениями которого являются *TimeRangeFrom* и *TimeRangeTo*.|


> [AZURE.NOTE] Учетные данные **AzureStorageConnection** должны быть правильными. **BlobContainer** должен быть создан до попытки передачи.

## Загрузка данных из хранилища BLOB-объектов Azure

Для загрузки данных из хранилища BLOB-объектов Azure в локальное хранилище служб SSIS используйте экземпляр [задачи отправки BLOB-объектов Azure](https://msdn.microsoft.com/library/mt146779.aspx).

##Более сложные сценарии SSIS-Azure
Ранее в этой статье отмечалось, что с помощью пакета дополнительных компонентов SSIS можно обрабатывать более сложные потоки, используя упаковку задач. Например, данные большого двоичного объекта можно передать непосредственно в кластер HDInsight. После этого полученный результат можно скачать обратно в большой двоичный объект, а затем — в локальное хранилище. Службы SSIS могут запускать задания Hive и Pig в кластере HDInsight с помощью дополнительных соединительных служб SSIS.

- Чтобы запустить сценарий Hive в кластере Azure HDInsight с помощью служб SSIS, используйте [задачу Hive Azure HDInsight](https://msdn.microsoft.com/library/mt146771.aspx).
- Чтобы запустить сценарий Pig в кластере Azure HDInsight с помощью служб SSIS, используйте [задачу Pig Azure HDInsight](https://msdn.microsoft.com/library/mt146781.aspx).

<!---HONumber=AcomDC_0921_2016-->