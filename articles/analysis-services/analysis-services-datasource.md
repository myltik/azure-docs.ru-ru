---
title: Источники данных, поддерживаемые в службах Azure Analysis Services | Документы Майкрософт
description: Описание источников данных, поддерживаемых для моделей данных в службах Azure Analysis Services.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/05/2018
ms.author: owend
ms.openlocfilehash: 16bf333fe18c912db7cb81e74fce29960b0728d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Источники данных, поддерживаемые в службах Azure Analysis Services

Источники данных и соединители, представленные в мастере получения или импорта данных в Visual Studio, отображаются как для служб Azure Analysis Services, так и для служб SQL Server Analysis Services. Однако в Azure Analysis Services поддерживаются не все показанные источники данных и соединители. Типы источников данных, к которым можно подключиться, зависят от многих факторов, таких как уровень совместимости модели, доступные соединители данных, тип аутентификации, поставщики и поддержка локального шлюза данных. 

## <a name="azure-data-sources"></a>Источники данных Azure

|Источник данных  |В памяти  |DirectQuery  |
|---------|---------|---------|
|Базы данных SQL Azure     |   Yes      |    Yes      |
|Хранилище данных SQL Azure     |   Yes      |   Yes       |
|Хранилище BLOB-объектов Azure*     |   Yes       |    Нет       |
|Хранилище таблиц Azure*    |   Yes       |    Нет       |
|Azure Cosmos DB*     |  Yes        |  Нет         |
|Azure Data Lake Store*     |   Yes       |    Нет       |
|Azure HDInsight HDFS*     |     Yes     |   Нет        |
|Azure HDInsight Spark*     |   Yes       |   Нет        |
||||

\* Только для табличных моделей 1400.

**Поставщик**   
Для размещенных в памяти моделей и моделей DirectQuery, подключающихся к источникам данных Azure, используется поставщик данных .NET Framework для SQL Server.

## <a name="on-premises-data-sources"></a>Локальные источники данных

Для подключения к локальным источникам данных с сервера автономной системы Azure требуется локальный шлюз. При использовании шлюза требуются 64-разрядные поставщики.

### <a name="in-memory-and-directquery"></a>Размещение в памяти и DirectQuery

|Источник данных | Поставщик с размещением в памяти | Поставщик DirectQuery |
|  --- | --- | --- |
| SQL Server; |SQL Server Native Client 11.0, поставщик Microsoft OLE DB для SQL Server, поставщик данных .NET Framework для SQL Server | Поставщик данных .NET Framework для SQL Server |
| хранилище данных SQL Server. |SQL Server Native Client 11.0, поставщик Microsoft OLE DB для SQL Server, поставщик данных .NET Framework для SQL Server | Поставщик данных .NET Framework для SQL Server |
| Oracle |Поставщик Microsoft OLE DB для Oracle, поставщик данных Oracle для .NET |Поставщик данных Oracle для .NET | |
| Teradata |Поставщик OLE DB для Teradata, поставщик данных Teradata для .NET |Поставщик данных Teradata для .NET | |
| | | |

### <a name="in-memory-only"></a>Только в памяти

|Источник данных  |  
|---------|---------|
|База данных Access     |  
|Active Directory*     |  
|службы Analysis Services     |  
|Система платформы аналитики     |  
|Dynamics CRM*     |  
|Книга Excel     |  
|Exchange*     |  
|Папка*     | 
|Документ JSON*     |  
|Строки из двоичного файла*     | 
|База данных MySQL     | 
|Веб-канал OData*     |  
|Запрос ODBC     | 
|OLE DB     |   
|База данных SQL Postgre*    | 
|SAP HANA*    |  
|SAP Business Warehouse*    |  
|SharePoint*     |   
|База данных Sybase     |  
|Таблица XML*    |  
|||
 
\* Только для табличных моделей 1400.

## <a name="specifying-a-different-provider"></a>Указание другого поставщика

Моделям данных в службах Azure Analysis Services могут требоваться разные поставщики данных при подключении к определенным источникам данных. В некоторых случаях табличные модели, которые подключаются к источникам данных с помощью собственных поставщиков, таких как собственный клиент SQL Server (SQLNCLI11), могут возвращать ошибку. При использовании собственных поставщиков, отличных от SQLOLEDB, может появиться сообщение об ошибке: **The provider 'SQLNCLI11.1' is not registered** (Поставщик SQLNCLI11.1 не зарегистрирован). Если же при наличии модели DirectQuery, подключающейся к локальным источникам данных, используются собственные поставщики, может появиться сообщение об ошибке: **Error creating OLE DB row set. Incorrect syntax near 'LIMIT'** (Ошибка при создании набора строк OLE DB. Неверный синтаксис рядом с "LIMIT").

При переносе локальной табличной модели служб SQL Server Analysis Services в службы Azure Analysis Services может потребоваться изменить поставщика.

**Указание поставщика**

1. В разделе SSDT > **Tabular Model Explorer (Обозреватель табличных моделей)** > **Data Sources (Источники данных)** щелкните правой кнопкой подключение к источнику данных и выберите **Edit Data Source (Изменить источник данных)**.
2. В окне **Edit Connection** (Изменение подключения) щелкните **Advanced** (Дополнительно), чтобы открыть окно дополнительных свойств.
3. В разделе **Set Advanced Properties (Настройка дополнительных свойств)** > **Providers (Поставщики)** выберите соответствующего поставщика.

## <a name="impersonation"></a>Олицетворение
В некоторых случаях может быть необходимо указать другую учетную запись олицетворения. Учетную запись олицетворения можно указать в Visual Studio (SSDT) или SSMS.

Для локальных источников данных:

* При использовании проверки подлинности SQL олицетворением должна быть учетная запись службы.
* При использовании проверки подлинности Windows задайте пользователя и пароль Windows. Для SQL Server проверка подлинности Windows с использованием конкретной учетной записи олицетворения поддерживается только для моделей данных в памяти.

Для облачных источников данных

* При использовании проверки подлинности SQL олицетворением должна быть учетная запись службы.

## <a name="next-steps"></a>Дополнительная информация
[Локальный шлюз](analysis-services-gateway.md)   
[Управление службами Analysis Services](analysis-services-manage.md)   

