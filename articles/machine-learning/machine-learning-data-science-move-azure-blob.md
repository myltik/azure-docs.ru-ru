<properties
	pageTitle="Перемещение данных в хранилище больших двоичных объектов Azure и из него | Microsoft Azure"
	description="Перемещение данных в хранилище больших двоичных объектов Azure и из него"
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
	ms.author="bradsev;sachouks" />

# Перемещение данных в хранилище больших двоичных объектов Azure и из него

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Сведения о технологиях, которые используются для перемещения данных в хранилище больших двоичных объектов Azure и из него, см. по следующим ссылкам:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
Выбор метода зависит от сценария. Статья [Сценарии для расширенной аналитики в Машинном обучении Azure](machine-learning-data-science-plan-sample-scenarios.md) поможет определить ресурсы, необходимые для различных рабочих процессов обработки и анализа данных в рамках расширенного аналитического процесса.

> [AZURE.NOTE] Полное описание базовых принципов использования хранилища BLOB-объектов Azure см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).

В качестве альтернативы можно использовать [фабрику данных Azure](https://azure.microsoft.com/services/data-factory/) для выполнения следующих действий:

- создание и планирование конвейера, который скачивает данные из хранилища BLOB-объектов Azure;
- передача данных в опубликованную веб-службу Машинного обучения Azure;
- получение результатов прогнозной аналитики;
- отправка результатов в хранилище.

Дополнительные сведения см. в статье [Создание прогнозирующих конвейеров с помощью фабрик данных Azure и машинного обучения Azure](../data-factory/data-factory-azure-ml-batch-execution-activity.md).

## Предварительные требования

Для выполнения указаний в этом документе у вас должна быть подписка Azure, учетная запись хранения и соответствующий ключ к хранилищу данных для этой учетной записи. Чтобы отправлять и скачивать данные, необходимо знать имя учетной записи хранения Azure и ее ключ.

- Сведения о настройке подписки Azure см. на странице [30-дневной бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).
- Указания по созданию учетной записи хранения и получению учетной записи и сведений о ключах см. в разделе [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md).

<!---HONumber=AcomDC_0921_2016-->