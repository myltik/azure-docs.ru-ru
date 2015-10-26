<properties 
	pageTitle="Перемещение данных в хранилище больших двоичных объектов Azure и из него | Microsoft Azure" 
	description="Перемещение данных в хранилище больших двоичных объектов Azure и из него" 
	services="machine-learning,storage" 
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
	ms.date="10/12/2015" 
	ms.author="bradsev;sunliangms;sachouks;mohabib" />

# Перемещение данных в хранилище больших двоичных объектов Azure и из него

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

## Введение

В этом разделе описаны три методики перемещения данных в хранилище больших двоичных объектов Azure и из него.

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

Выбор метода зависит от сценария. Статья [Процесс обработки и анализа данных в облаке в Машинном обучении Azure](../machine-learning-data-science-plan-sample-scenarios.md) помогает определить ресурсы, необходимые для различных процедур обработки и анализа данных в рамках расширенного аналитического процесса.

> [AZURE.NOTE]Полное описание базовых принципов использования хранилища больших двоичных объектов Azure см. в статьях [Основы использования больших двоичных объектов Azure](../storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).

> [AZURE.TIP]В качестве альтернативы можно воспользоваться [фабрикой данных Azure](https://azure.microsoft.com/ru-RU/services/data-factory/), чтобы создать и назначить канал для загрузки данных из хранилища больших двоичных объектов Azure, их передачи в опубликованную службу машинного обучения Azure, получения прогнозных аналитических результатов и их отправки обратно в хранилище. Дополнительные сведения см. в статье [Создание прогнозирующих конвейеров с помощью фабрик данных Azure и машинного обучения Azure](../data-factory/data-factory-create-predictive-pipelines.md).

## Предварительные требования

Для выполнения указаний в этом документе у вас должна быть подписка Azure, учетная запись хранения и соответствующий ключ к хранилищу данных для этой учетной записи. Чтобы отправлять и скачивать данные, необходимо знать имя учетной записи хранения Azure и ее ключ.

- Чтобы настроить подписку Azure, см. раздел [Бесплатный ознакомительный период в один месяц](https://azure.microsoft.com/ru-RU/pricing/free-trial/).
- Указания по созданию учетной записи хранения и получению учетной записи и сведений о ключах см. в разделе [Об учетных записях хранения Azure](../storage-create-storage-account.md).




 

<!---HONumber=Oct15_HO3-->