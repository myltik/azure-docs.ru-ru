---
title: "Мониторинг и устранение неполадок переноса данных (база данных Stretch) | Документация Майкрософт"
description: "Узнайте, как отслеживать состояние переноса данных."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 2341d446-9909-4694-8bb8-d288582daf54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4efcb1b30f002d7baecaa46f9994dfbc9ad42dee


---
# <a name="monitor-and-troubleshoot-data-migration-stretch-database"></a>Мониторинг и устранение неполадок переноса данных (база данных Stretch)
Чтобы отслеживать перенос данных в мониторе базы данных Stretch, выберите **Задачи | Растяжение | Мониторинг** для базы данных в SQL Server Management Studio.

## <a name="check-the-status-of-data-migration-in-the-stretch-database-monitor"></a>Проверка состояния переноса данных в мониторе базы данных Stretch
Выберите **Задачи | Растяжение | Мониторинг** для базы данных в SQL Server Management Studio, чтобы открыть монитор базы данных Stretch и отслеживать перенос данных.

* В верхней части монитора отображаются общие сведения о базе данных SQL Server, совместимой со Stretch, и удаленной базе данных Azure.
* В нижней части монитора отображается состояние переноса данных для каждой таблицы, совместимой со Stretch, в базе данных.

![Монитор базы данных Stretch][StretchMonitorImage1]

## <a name="a-namemigrationacheck-the-status-of-data-migration-in-a-dynamic-management-view"></a><a name="Migration"></a>Проверка состояния переноса данных в динамическом административном представлении
Откройте динамическое административное представление **sys.dm\_db\_rda\_migration\_status**, чтобы узнать, сколько пакетов и строк данных было перенесено. Дополнительные сведения см. в статье [sys.dm_db_rda_migration_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="a-namefirewallatroubleshoot-data-migration"></a><a name="Firewall"></a>Устранение неполадок переноса данных
**Строки из таблицы с поддержкой Stretch не переносятся в Azure. В чем заключается проблема?**

На перенос может повлиять несколько проблем. Проверьте следующее.

* Проверьте сетевое подключение к компьютеру с SQL Server.
* Убедитесь, что брандмауэр Azure не запрещает SQL Server подключаться к удаленной конечной точке.
* В динамическом административном представлении **sys.dm\_db\_rda\_migration\_status** проверьте состояние последнего пакета. Если произошла ошибка, проверьте значения error\_number, error\_state и error\_severity для пакета.
  
  * Дополнительные сведения о представлении см. в статье [sys.dm_db_rda_migration_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).
  * Дополнительные сведения о содержимом сообщения об ошибке SQL Server см. в статье [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

**Брандмауэр Azure блокирует подключения из моего локального сервера.**

Может потребоваться добавить правило в параметрах брандмауэра Azure на сервере Azure, чтобы разрешить SQL Server взаимодействовать с удаленным сервером Azure.

## <a name="see-also"></a>См. также
[Управляйте растяжением баз данных и устраняйте неполадки](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png



<!--HONumber=Dec16_HO2-->


