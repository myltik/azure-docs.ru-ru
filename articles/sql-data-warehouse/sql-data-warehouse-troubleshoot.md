---
title: Устранение неполадок хранилища данных SQL Azure | Документация Майкрософт
description: Диагностика и устранение неполадок хранилища данных SQL Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 27445eb754a5e985485db101c9d0fe1ba8aa2451
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525253"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Устранение неполадок хранилища данных SQL Azure
В этой статье даны рекомендации по устранению распространенных неполадок.

## <a name="connecting"></a>Подключение
| Проблема | Способы устранения: |
|:--- |:--- |
| Ошибка входа пользователя "NT AUTHORITY\ANONYMOUS LOGON". (Microsoft SQL Server, ошибка: 18456) |Эта ошибка возникает, когда пользователь AAD пытается подключиться к базе данных master, в которой нет соответствующей учетной записи пользователя.  Для устранения этой проблемы либо укажите хранилище данных SQL, к которому необходимо подключиться, во время подключения, либо добавьте учетную запись пользователя в базу данных master.  Дополнительные сведения см. в статье [Защита базы данных в хранилище данных SQL][Security overview]. |
| Субъект-сервер "MyUserName" не может получить доступ к базе данных master в текущем контексте безопасности. Не удается открыть пользовательскую базу данных по умолчанию. Вход в систему не выполнен. Пользователю "MyUserName" не удалось войти в систему. (Microsoft SQL Server, ошибка: 916) |Эта ошибка возникает, когда пользователь AAD пытается подключиться к базе данных master, в которой нет соответствующей учетной записи пользователя.  Для устранения этой проблемы либо укажите хранилище данных SQL, к которому необходимо подключиться, во время подключения, либо добавьте учетную запись пользователя в базу данных master.  Дополнительные сведения см. в статье [Защита базы данных в хранилище данных SQL][Security overview]. |
| Ошибка CTAIP |Эта ошибка может возникнуть, когда имя для входа создано в базе данных master SQL Server, но не создано в базе данных хранилища данных SQL.  Если возникла эта ошибка, ознакомьтесь со статьей [Защита базы данных в хранилище данных SQL][Security overview].  В этой статье объясняется, как создать имя для входа и пользователя в базе данных master, а затем создать пользователя в базе данных хранилища данных SQL. |
| Заблокировано брандмауэром |Базы данных SQL Azure защищены брандмауэрами на уровне сервера и базы данных. Это гарантирует, что доступ к базам данных возможен только с известных IP-адресов. Брандмауэры являются безопасными по умолчанию. Это означает, что перед подключением необходимо прямо разрешить доступ для IP-адреса или диапазона IP-адресов.  Чтобы настроить брандмауэр для предоставления доступа, выполните указания в разделе [Создание правила брандмауэра на уровне сервера с помощью портала Azure][Configure server firewall access for your client IP] статьи [Создание хранилища данных SQL Azure][Provisioning instructions]. |
| Не удается подключиться с помощью средства или драйвера |В хранилище данных SQL для запроса данных рекомендуется использовать [SSMS][SSMS], [SSDT для Visual Studio][SSDT for Visual Studio] или [sqlcmd][sqlcmd]. Дополнительные сведения о необходимых драйверах и подключении к хранилищу данных SQL см. статьях [Драйверы для хранилища данных SQL Azure][Drivers for Azure SQL Data Warehouse] и [Подключение к хранилищу данных SQL Azure][Connect to Azure SQL Data Warehouse]. |

## <a name="tools"></a>Средства
| Проблема | Способы устранения: |
|:--- |:--- |
| В обозревателе объектов Visual Studio отсутствуют пользователи AAD |Это известная проблема.  Сведения о пользователях можно просмотреть в файле [sys.database_principals][sys.database_principals].  Дополнительные сведения об использовании Azure Active Directory с хранилищем данных SQL см. в статье [Аутентификация в хранилище данных SQL Azure][Authentication to Azure SQL Data Warehouse]. |
|Создание сценариев вручную, использование мастера создания сценариев или подключение с помощью SSMS происходит медленно, с "зависаниями" или ошибками.| Убедитесь, что пользователи были созданы в базе данных master. Коме того, убедитесь, что в параметрах создания сценариев заданы выпуск ядра "Выпуск Microsoft Azure SQL Data Warehouse Edition" и тип модуля "База данных SQL Microsoft Azure".|

## <a name="performance"></a>Производительность
| Проблема | Способы устранения: |
|:--- |:--- |
| Устранение проблем с производительностью запросов |Если вы пытаетесь устранить проблему конкретного запроса, для начала ознакомьтесь со статьей [Мониторинг рабочей нагрузки с помощью динамических административных представлений][Learning how to monitor your queries]. |
| Низкая производительность и неправильные планы запросов в результате отсутствия статистики |Самая распространенная причина низкой производительности — отсутствие статистики таблиц.  Дополнительные сведения о создании статистики и о том, почему она очень важна для производительности, см. в статье [Управление статистикой таблиц в хранилище данных SQL][Statistics]. |
| Низкий уровень параллелизма и помещение запросов в очередь |Чтобы обеспечить выделение памяти с учетом параллелизма, важно понимать принципы [управления рабочими нагрузками][Workload management]. |
| Реализация рекомендаций |Лучше всего начать изучение способов повышения производительности запросов со статьи [Рекомендации по использованию хранилища данных SQL Azure][SQL Data Warehouse best practices]. |
| Повышение производительности за счет масштабирования |Иногда повысить производительность запросов позволяет простое увеличение вычислительной мощности, выделяемой для выполнения запросов, за счет [масштабирования хранилища данных SQL][Scaling your SQL Data Warehouse]. |
| Низкая производительность запросов как результат низкого качества индексов |Иногда выполнение запросов может замедляться из-за [низкого качества индекса columnstore][Poor columnstore index quality].  Дополнительные сведения см. в статье об индексировании таблиц в разделе, посвященном [повышению качества сегментов за счет перестроения индексов][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Управление системой
| Проблема | Способы устранения: |
|:--- |:--- |
| Сообщение 40847: не удалось выполнить операцию, так как сервер достиг допустимой квоты в 45 000 единиц транзакций базы данных (DTU). |Либо уменьшите значение [DWU][DWU] создаваемой базы данных, либо [запросите увеличение квоты][request a quota increase]. |
| Анализ использования пространства |Сведения об использовании пространства в системе см. в разделе о [запросах размера таблицы][Table sizes]. |
| Справка по управлению таблицами |Справочную информацию об управлении таблицами см. в статье [Общие сведения о таблицах в хранилище данных SQL][Overview].  Дополнительные сведения см. в статьях, посвященных [типам данных таблиц][Data types], [распределению][Distribute], [индексированию][Index], [секционированию][Partition], [управлению статистикой таблиц][Statistics] и [временным таблицам][Temporary]. |
|На портале Azure не обновляется индикатор хода прозрачного шифрования данных (TDE)|Состояние прозрачного шифрования данных можно узнать с помощью [PowerShell](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption).|

## <a name="polybase"></a>PolyBase
| Проблема | Способы устранения: |
|:--- |:--- |
| Ошибка загрузки из-за большого размера строк |В настоящее время для Polybase не поддерживаются строки большого размера.  Таким образом, если ваша таблица содержит данные типа VARCHAR(MAX), NVARCHAR(MAX) или VARBINARY(MAX), для их загрузки нельзя использовать внешние таблицы.  Строки больших размеров можно загружать только с помощью фабрики данных Azure (с использованием BCP), Azure Stream Analytics, Integration Services, программы BCP или класса .NET SQLBulkCopy. Поддержка строк большого размера для PolyBase будет добавлена в будущем выпуске. |
| Ошибка загрузки таблицы с типом данных MAX с использованием BCP |Это известная проблема. Для ее решения в некоторых сценариях нужно поместить данные VARCHAR(MAX), NVARCHAR(MAX) или VARBINARY(MAX) в конец таблицы.  Попытайтесь переместить столбцы с типом данных MAX в конец таблицы. |

## <a name="differences-from-sql-database"></a>Отличия от базы данных SQL
| Проблема | Способы устранения: |
|:--- |:--- |
| Неподдерживаемые функции базы данных SQL |Ознакомьтесь с разделом [Неподдерживаемые функции таблиц][Unsupported table features]. |
| Неподдерживаемые типы данных базы данных SQL |Ознакомьтесь с разделом [Неподдерживаемые типы данных][Unsupported data types]. |
| Ограничения относительно инструкций DELETE и UPDATE |Ознакомьтесь с обходными решениями для инструкций [UPDATE][UPDATE workarounds] и [DELETE][DELETE workarounds], а также с [использованием инструкции CTAS для обхода неподдерживаемого синтаксиса UPDATE и DELETE][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Инструкция MERGE не поддерживается |Ознакомьтесь с [обходными решениями для инструкции MERGE][MERGE workarounds]. |
| Ограничения хранимых процедур |Ознакомьтесь с [ограничениями хранимых процедур][Stored procedure limitations]. |
| Определяемые пользователем функции не поддерживают инструкции SELECT |Это текущее ограничение определяемых пользователем функций.  Сведения о поддерживаемом синтаксисе см. в статье, посвященной инструкции [CREATE FUNCTION][CREATE FUNCTION]. |

## <a name="next-steps"></a>Дополнительная информация
Ниже перечислены некоторые ресурсы, которые можно использовать для поиска решения вашей проблемы.

* [Блоги]
* [Запросы функций]
* [Видеоролики]
* [Блоги группы CAT]
* [Создание запроса в службу поддержки]
* [Форум MSDN]
* [Форум Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Создание запроса в службу поддержки]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]:/sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Блоги]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Блоги группы CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Запросы функций]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Форум MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Форум Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Видеоролики]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
