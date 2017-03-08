---
title: "Проверка производительности и масштабирования DocumentDB | Документация Майкрософт"
description: "Узнайте, как выполнить проверку производительности и масштабирования с Azure DocumentDB"
keywords: "тестирование производительности"
services: documentdb
author: arramac
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 6b82132b24fb2be3f0d8ab5112c712c27edb733e
ms.openlocfilehash: 4d7754e73abedb704b81fa0001e1957422bde1b3


---
# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Проверка производительности и масштабирования с помощью Azure DocumentDB
Проверка производительности и масштабирования — ключевой этап в разработке приложения. Для многих приложений уровень баз данных оказывает значительное влияние на общую производительность и масштабируемость. Поэтому он имеет огромное значение при проверке производительности. Система [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) разработана с расчетом на эластичное масштабирование и предсказуемую производительность. Поэтому она отлично подходит для приложений, требующих высокую производительность для уровня базы данных. 

Эта статья предназначена для специалистов, разрабатывающих наборы тестов производительности для рабочих нагрузок DocumentDB или оценивающих DocumentDB для сценариев с высокопроизводительными приложениями. Эта статья уделяет основное внимание изолированной проверке производительности базы данных, но также включает рекомендации для приложений в рабочей среде.

После прочтения этой статьи вы сможете ответить на следующие вопросы:   

* Где найти образец клиентского приложения .NET для проверки производительности Azure DocumentDB? 
* Как добиться высокой пропускной способности с помощью Azure DocumentDB из клиентского приложения?

Чтобы приступить к работе с кодом, скачайте проект со страницы [Пример для тестирования производительности DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Цель этого приложения — продемонстрировать рекомендации, направленные на повышение производительности службы DocumentDB при использовании небольшого числа клиентских компьютеров. Демонстрация максимальной емкости не является целью, так как служба имеет безграничные возможности масштабирования.
> 
> 

Если вам нужны параметры конфигурации на стороне клиента для повышения производительности DocumentDB, ознакомьтесь с [рекомендациями по оптимизации производительности DocumentDB](documentdb-performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Запуск приложения для тестирования производительности
Самый удобный способ начать работу — скомпилировать и запустить пример .NET, как показано ниже. Вы также можете просмотреть исходный код и реализовывать аналогичные настройки в ваших клиентских приложениях.

**Шаг 1**. Скачайте проект со страницы [Пример для тестирования производительности DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) или создайте разветвление в репозитории GitHub.

**Шаг 2**. Измените параметры EndpointUrl, AuthorizationKey, CollectionThroughput и DocumentTemplate (необязательно) в файле App.config.

> [!NOTE]
> Перед подготовкой коллекций с высокой пропускной способностью просмотрите [страницу с ценами](https://azure.microsoft.com/pricing/details/documentdb/) , чтобы рассчитать затраты для каждой коллекции. В DocumentDB использование места на диске и пропускной способности тарифицируются независимо друг от друга на почасовой основе, поэтому вы сможете сэкономить, понизив или отключив пропускную способность для своих коллекций DocumentDB после тестирования.
> 
> 

**Шаг 3**. Скомпилируйте и запустите консольное приложение из командной строки. Вы должны увидеть следующий результат:

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**Шаг 4 (при необходимости)**. Пропускная способность (единицы запроса в секунду), определенная с помощью инструмента, должна быть не меньше подготовленной пропускной способности коллекции. Если этого не происходит, увеличение степени параллелизма (DegreeOfParallelism) с небольшим шагом может помочь в достижении предельного значения. Если пропускная способность клиентского приложения останавливается на определенном уровне, то запуск нескольких экземпляров приложения на одном или разных компьютерах поможет достичь предела подготовленной пропускной способности для нескольких экземпляров. Если вам требуется помощь на этом этапе, отправьте сообщение на электронный адрес [ или отправьте запрос в службу поддержки на askdocdb@microsoft.comпортале Azure](https://portal.azure.com).

После запуска приложения вы можете опробовать различные [политики индексации](documentdb-indexing-policies.md) и [уровни согласованности](documentdb-consistency-levels.md), чтобы понять, как они влияют на производительность и задержки. Вы также можете просмотреть исходный код и реализовывать аналогичные настройки в ваших тестовых наборах или приложениях в рабочей среде.

## <a name="next-steps"></a>Дальнейшие действия
В этой статье мы рассмотрели способы тестирования производительности и масштабируемости с помощью консольного приложения .NET DocumentDB. Для получения дополнительных сведений о работе с DocumentDB используйте следующие ссылки.

* [Пример для тестирования производительности DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Performance tips for DocumentDB (Рекомендации по оптимизации производительности DocumentDB)](documentdb-performance-tips.md)
* [Секционирование на стороне сервера в DocumentDB](documentdb-partition-data.md)
* [Коллекции и уровни производительности DocumentDB](documentdb-performance-levels.md)
* [Документация по пакету SDK .NET для DocumentDB на сайте MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Примеры DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
* [Блог DocumentDB с советами по повышению производительности](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)




<!--HONumber=Jan17_HO3-->


