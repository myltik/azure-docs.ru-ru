---
title: "Общие сведения о средствах управления для базы данных SQL | Документация Майкрософт"
description: "Сравнение средств и параметров для управления Базой данных SQL Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5adb83a7b8e19ca78dd4d837b497e1ceb4444f86


---
# <a name="overview-management-tools-for-sql-database"></a>Обзор: средства управления для Базы данных SQL
В этом разделе рассматриваются и сравниваются средства и параметры для управления базами данных SQL Azure, чтобы вы могли выбрать для себя подходящий инструмент для работы и бизнеса. Выбор правильного средства зависит от количества баз данных, которыми вы управляете, выполняемых задач, а также то того, как часто вы их выполняете.

## <a name="azure-portal"></a>Портал Azure
[Портал Azure](https://portal.azure.com) — это веб-приложение, в котором можно создавать, обновлять и удалять базы данных и логические серверы, а также отслеживать работу базы данных. Он превосходно подойдет для тех, кто только знакомится с Azure, или управляет небольшим количеством баз данных, а также в случае, если требуется оперативно выполнить какую-либо задачу.

Дополнительные сведения об использовании портала см. в разделе [Управление базами данных SQL Azure с помощью портала Azure](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio и SQL Server Data Tools в Visual Studio
SQL Server Management Studio (SSMS) и SQL Server Data Tools (SSDT) представляют собой клиентские инструменты, которые выполняются на компьютере и позволяют управлять базой данных в облаке и разрабатывать ее. Если вы не новичок в разработке приложений и уже знакомы с Visual Studio или другими интегрированными средами разработки (IDE), [предлагаем обратить внимание на SSDT в составе Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Многие администраторы баз данных знакомы с решением SSMS, которое можно использовать с базами данных SQL Azure. [Скачайте последнюю версию SSMS](https://msdn.microsoft.com/library/mt238290) и всегда используйте последнюю версию решения при работе с базой данных SQL Azure. Дополнительные сведения об управлении базами данных SQL Azure с помощью SSMS и SSDT см. в статье [Управление базой данных SQL Azure с помощью SQL Server Management Studio](sql-database-manage-azure-ssms.md).

> [!IMPORTANT]
> Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и базы данных SQL, всегда используйте последнюю версию [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) и [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx).
>  

## <a name="powershell"></a>PowerShell
Для управления базами данных и пулами эластичных баз данных, а также для автоматизации развертывания ресурсов Azure можно PowerShell. Мы рекомендуем использовать это средство для управления большим количеством баз данных и автоматизации развертывания и изменения ресурсов в рабочей среде.

Дополнительные сведения см. в разделе [Управление базой данных SQL Azure с помощью PowerShell](sql-database-manage-powershell.md).

## <a name="elastic-database-tools"></a>Инструменты эластичных баз данных
Используйте инструменты эластичных баз данных для выполнения следующих действий. 

* Выполнение сценария T-SQL для набора баз данных с помощью [задания обработки эластичных БД](sql-database-elastic-jobs-overview.md)
* Перемещение баз данных из мультитенантной модели в модель с одним клиентом с помощью [инструмента разбиения и объединения](sql-database-elastic-scale-overview-split-and-merge.md)
* Управление базами данных в модели с одним клиентом или мультитенантной модели с помощью [клиентской библиотеки эластичного масштабирования](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* [Служба автоматизации Azure](https://azure.microsoft.com/documentation/services/automation/)
* [Планировщик Azure](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Nov16_HO3-->


