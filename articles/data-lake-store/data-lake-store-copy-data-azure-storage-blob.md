<properties
   pageTitle="Копирование данных из больших двоичных объектов хранилища Azure в хранилище озера данных | Microsoft Azure"
   description="Использование средства AdlCopy для копирования данных из больших двоичных объектов хранилища Azure в хранилище озера данных"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>

# Копирование данных из больших двоичных объектов хранилища Azure в хранилище озера данных

Хранилище озера данных Azure предоставляет средство командной строки [AdlCopy](http://aka.ms/downloadadlcopy) для копирования данных **из больших двоичных объектов хранилища Azure в хранилище озера данных**. AdlCopy нельзя использовать для копирования данных из хранилища озера данных в большие двоичные объекты хранилища Azure.

Средство AdlCopy можно использовать двумя способами:

* **Автономный**, при котором для выполнения задачи используются ресурсы хранилища озера данных.
* **С помощью учетной записи аналитики озера данных**, при котором для выполнения операции копирования используются единицы, назначенные учетной записи аналитики озера данных. Этот вариант можно использовать, если вы хотите, чтобы задачи копирования выполнялись предсказуемым образом.

##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Включите свою подписку Azure** для общедоступной предварительной версии хранилища озера данных. См. [инструкции](data-lake-store-get-started-portal.md#signup).
- Контейнер **больших двоичных объектов хранилища Azure** с некоторыми данными.
- **Учетная запись аналитики озера данных Azure (необязательно)** — инструкции по созданию учетной записи хранилища озера данных см. в разделе [Приступая к работе с аналитикой озера данных Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md) .
- **Средство AdlCopy**. Установите средство AdlCopy по ссылке [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## Синтаксис средства AdlCopy

Используйте следующий синтаксис для работы со средством AdlCopy

	AdlCopy /Source <Blob source> /Dest <ADLS destination> /SourceKey <Key for Blob account> /Account <ADLA account> /Unit <Number of Analytics units>

Параметры синтаксиса описаны ниже:

| Параметр | Описание |
|-----------|------------|
| Источник | Указывает расположение исходных данных в хранилище больших двоичных объектов Azure. Источником может быть контейнер больших двоичных объектов или большой двоичный объект. |
| Dest | Указывает целевое хранилище озера данных для копирования. |
| SourceKey | Указывает ключ доступа к хранилищу для источника — хранилища больших двоичных объектов Azure. |
| Учетная запись | **Необязательно**. Используйте этот вариант, если хотите, чтобы для запуска задания копирования использовалась учетная запись аналитики озера данных Azure. Если при запуске средства указан параметр /Account, но не указана учетная запись аналитики озера данных Azure, то для запуска задания AdlCopy использует учетную запись по умолчанию. Кроме того, при использовании этого параметра необходимо добавить источник (хранилище больших двоичных Azure) и место назначения (хранилище озера данных Azure) в качестве источников данных для вашей учетной записи аналитики озера данных. |
| Units | Указывает количество единиц аналитики озера данных, которые будут использоваться для задания копирования. Этот параметр является обязательным, если вы указываете учетную запись аналитики озера данных с помощью параметра **/Account**.                                                                                                                                                                                                                                                                                                                                               



## Автономное использование средства AdlCopy

1. Откройте командную строку и перейдите в каталог, в который установлено средство AdlCopy, обычно `%HOMEPATH%\Documents\adlcopy`.

2. Выполните следующую команду, чтобы скопировать заданный большой двоичный объект из контейнера-источника в хранилище озера данных:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Например:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	Вам будет предложено ввести учетные данные для подписки Azure, в которой расположена учетная запись хранилища озера данных. Вы увидите результат, аналогичный приведенному ниже:

		Initializing Copy.
		Copy Started.
		...............
		0.00% data copied.
		. . .
		. . .
		100% data copied.
		Finishing copy.
		....
		Copy Completed.

1. Также можно скопировать все большие двоичные объекты из одного контейнера в учетную запись хранилища данных озера, используя следующую команду:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	Например:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==



## Использование AdlCopy с учетной записью аналитики озера данных

Вы также можете воспользоваться своей учетной записью аналитики озера данных для запуска задания AdlCopy для копирования данных из хранилища больших двоичных объектов Azure в хранилище озера данных. Этот вариант обычно используется, если объем перемещаемых данных измеряется гигабайтами или терабайтами и вам необходима лучшая и прогнозируемая производительность.

Для использования учетной записи аналитики озера данных с AdlCopy необходимо добавить источник (хранилище больших двоичных Azure) и место назначения (хранилище озера данных Azure) в качестве источников данных для вашей учетной записи аналитики озера данных. Инструкции по добавлению дополнительных источников данных в учетную записи аналитики озера данных см. в разделе [Управление источниками данных аналитики озера данных](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

Выполните следующую команду:

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Например:

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeaccount /Units 2

## Выставление счетов

* При автономном использовании средства AdlCopy исходящий трафик для перемещения данных будет тарифицироваться в том случае, если источник учетной записи хранилища Azure и хранилище озера данных находятся в разных регионах.

* При использовании средства AdlCopy с вашей учетной записью аналитики озера будут применены стандартные [тарифные ставки аналитики озера данных](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

## Рекомендации по использованию AdlCopy

* AdlCopy (для версии 1.0.4) поддерживает копирование данных из источников, которые совокупно содержат более тысяч файлов и папок. Тем не менее, если при копировании большого набора данных возникают проблемы, то вы можете распределить файлы и папки в разных вложенных папках и использовать путь к этим вложенным папкам в качестве источника.

## Дальнейшие действия

- [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
- [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0713_2016-->