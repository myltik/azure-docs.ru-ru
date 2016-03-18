<properties
	pageTitle="Мониторинг и устранение неполадок переноса данных (база данных Stretch) | Microsoft Azure"
	description="Узнайте, как отслеживать состояние переноса данных."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Мониторинг и устранение неполадок переноса данных (база данных Stretch)

Чтобы отслеживать перенос данных в мониторе базы данных Stretch, выберите **Задачи | Растяжение | Мониторинг** для базы данных в SQL Server Management Studio.

## Проверка состояния переноса данных в мониторе базы данных Stretch
Выберите **Задачи | Растяжение | Мониторинг** для базы данных в SQL Server Management Studio, чтобы открыть монитор базы данных Stretch и отслеживать перенос данных.

-   В верхней части монитор отображаются общие сведения о базе данных SQL Server, совместимой со Stretch, и удаленной базе данных Azure.

-   В нижней части монитора отображается состояние переноса данных каждой таблицы, совместимой со Stretch, в базе данных.

![Монитор базы данных Stretch][StretchMonitorImage1]

## <a name="Migration"></a>Проверка состояния переноса данных в динамическом административном представлении
Откройте динамическое административное представление **sys.dm\_db\_rda\_migration\_status**, чтобы увидеть, сколько пакетов и строк данных было перенесено. Дополнительные сведения см. в разделе [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="Firewall"></a>Устранение неполадок переноса данных
На перенос может повлиять несколько проблем. Проверьте следующее.

-   Проверьте сетевое подключение к компьютеру с SQL Server.

-   В динамическом административном представлении **sys.dm\_db\_rda\_migration\_status** проверьте состояние последнего пакета. Если произошла ошибка, проверьте значения error\_number, error\_state и error\_severity для пакета.

    -   Дополнительные сведения о представлении см. в разделе [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

    -   Дополнительные сведения о содержимом сообщения об ошибке SQL Server см. в разделе [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

## См. также
[Manage and troubleshoot Stretch Database](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png

<!---HONumber=AcomDC_0302_2016-->