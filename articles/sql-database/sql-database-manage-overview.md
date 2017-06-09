---
title: "Инструменты управления и разработки для Базы данных SQL Azure | Документация Майкрософт"
description: "Введение в инструменты управления и разработки и их параметры для базы данных SQL Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: monitor & manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 9b0a3a314e576db2133e5c63fada43bb11a4e520
ms.contentlocale: ru-ru
ms.lasthandoff: 03/04/2017


---
# <a name="overview-tools-to-manage--develop-with-azure-sql-database"></a>Обзор: инструменты управления и разработки для Базы данных SQL Azure
В этом разделе рассказывается об инструментах управления и разработки для баз данных SQL Azure.

> [!IMPORTANT]
> Этот набор документации содержит краткие руководства, примеры и практические руководства, обучающие использованию инструментов, описываемых в следующих параграфах, для управления базой данных SQL Azure и разработки для нее. Используйте левую область навигации и фильтр для поиска конкретного содержимого для портала Azure, PowerShell и T-SQL.
>

## <a name="azure-portal"></a>Портал Azure
[Портал Azure](https://portal.azure.com) — это веб-приложение, в котором можно создавать, обновлять и удалять базы данных и логические серверы, а также отслеживать работу базы данных. Он превосходно подойдет для тех, кто только знакомится с Azure, или управляет небольшим количеством баз данных, а также в случае, если требуется оперативно выполнить какую-либо задачу.

## <a name="sql-server-management-studio-and-transact-sql"></a>SQL Server Management Studio и Transact-SQL
SQL Server Management Studio (SSMS) — это клиентский инструмент, который выполняется на вашем компьютере и позволяет управлять базой данных в облаке с помощью Transact-SQL. Многие администраторы баз данных знакомы с решением SSMS, которое можно использовать с базами данных SQL Azure. [Скачайте последнюю версию SSMS](https://msdn.microsoft.com/library/mt238290) и всегда используйте последнюю версию решения при работе с базой данных SQL Azure. 

> [!IMPORTANT]
> Всегда используйте последнюю версию [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290).
>  

## <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools в Visual Studio
SQL Server Data Tools (SSDT) — это клиентский инструмент, который выполняется на вашем компьютере и обеспечивает разработку для базы данных в облаке. Если вы не новичок в разработке приложений и уже знакомы с Visual Studio или другими интегрированными средами разработки (IDE), [предлагаем обратить внимание на SSDT в составе Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx).  

> [!IMPORTANT]
> Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базы данных SQL, всегда используйте последнюю версию [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx).
>  
## <a name="powershell"></a>PowerShell
Для управления базами данных и пулами эластичных баз данных, а также для автоматизации развертывания ресурсов Azure можно использовать PowerShell. Мы рекомендуем использовать это средство для управления большим количеством баз данных и автоматизации развертывания и изменения ресурсов в рабочей среде.

## <a name="elastic-database-tools"></a>Инструменты эластичных баз данных
Используйте инструменты эластичных баз данных для выполнения следующих действий. 

* Выполнение сценария T-SQL для набора баз данных с помощью [задания обработки эластичных БД](sql-database-elastic-jobs-overview.md)
* Перемещение баз данных из мультитенантной модели в модель с одним клиентом с помощью [инструмента разбиения и объединения](sql-database-elastic-scale-overview-split-and-merge.md)
* Управление базами данных в модели с одним клиентом или мультитенантной модели с помощью [клиентской библиотеки эластичного масштабирования](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* [Служба автоматизации Azure](https://azure.microsoft.com/documentation/services/automation/)
* [Планировщик Azure](https://azure.microsoft.com/documentation/services/scheduler/)


