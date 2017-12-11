---
title: "Пошаговое копирование данных с помощью фабрики данных Azure | Документация Майкрософт"
description: "В этих руководствах описывается добавочное копирование данных из исходного хранилища данных в целевое хранилище данных. В первом руководстве данные копируются из одной таблицы. "
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/05/2017
ms.author: shlo
ms.openlocfilehash: 2ae6cb42685dfb227bd75f83e73dfdf646ab909f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2017
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Добавочная загрузка данных из исходного хранилища данных в целевое

В решениях для интеграции данных добавочная (разностная) загрузка данных после начальной загрузки данных является широко используемым сценарием. В руководствах этого раздела показаны различные способы добавочной загрузки данных с помощью фабрики данных Azure версии 2.

## <a name="delta-data-loading-using-a-watermark"></a>Разностная загрузка данных с использованием предела
В этом случае следует определить предел в базе данных-источнике. Предел представляет собой столбец, содержащий метку времени последнего обновления или инкрементный ключ. Решение разностной загрузки загружает измененные данные между значением старого предела и значением нового предела. Рабочий процесс для этого подхода показан на следующей схеме: 

![Рабочий процесс использования предела](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Пошаговые инструкции представлены в следующих статьях: 

- [Пошаговая загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure](tutorial-incremental-copy-powershell.md)
- [Incrementally load data from multiple tables in SQL Server to Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md) (Добавочная загрузка данных из нескольких таблиц на сервере SQL Server в базу данных SQL)


## <a name="delta-data-loading-using-the-change-tracking-technology"></a>Разностная загрузка данных с использованием технологии отслеживания изменений
Технология отслеживания изменений — это упрощенное решение в SQL Server и Базе данных SQL Azure, которое предоставляет эффективный механизм отслеживания изменений для приложений. Эта технология позволяет приложению легко идентифицировать вставленные, обновленные или удаленные данные. 

Рабочий процесс для этого подхода показан на следующей схеме:

![Рабочий процесс для использования отслеживания изменений](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Пошаговые инструкции представлены в следующем руководстве: <br/>
[Incrementally load data from Azure SQL Database to Azure Blob Storage using change tracking information](tutorial-incremental-copy-change-tracking-feature-powershell.md) (Добавочная загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure с использованием технологии отслеживания изменений)


## <a name="next-steps"></a>Дальнейшие действия
Перейдите к следующему руководству: 

> [!div class="nextstepaction"]
>[Пошаговая загрузка данных из базы данных SQL Azure в хранилище BLOB-объектов Azure](tutorial-incremental-copy-powershell.md)
