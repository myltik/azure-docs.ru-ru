---
title: "Обзор возможностей базы данных SQL Azure | Документация Майкрософт"
description: "На этой странице представлен обзор логических серверов и баз данных SQL Azure, а также таблица со сведениями о поддержке функций и ссылки для каждой из них."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: 21be71a1b4c79ecec8af02d08f65c41128c5ef73
ms.openlocfilehash: 50a465f314909c10bc3c3f95be2d9dc377d433a7


---
# <a name="azure-sql-database-features"></a>Возможности базы данных SQL Azure
В этой статье представлен обзор логических серверов и баз данных SQL Azure, а также таблица со сведениями о поддержке функций и ссылки для каждой из них. 

## <a name="what-is-an-azure-sql-database-logical-server"></a>Общие сведения о логическом сервере базы данных SQL Azure
Логический сервер базы данных SQL Azure выступает в качестве центральной точки администрирования нескольких баз данных. В базе данных SQL сервер — это логическая конструкция, отличающаяся от экземпляра SQL Server, с которым вы могли работать в локальной среде. В частности, служба базы данных SQL не дает никаких гарантий, связанных с расположением баз данных относительно логических серверов, а также не предоставляет доступ или возможности на уровне экземпляра. Дополнительные сведения о логических серверах Azure SQL см. в [этой статье](sql-database-server-overview.md). 

## <a name="what-is-an-azure-sql-database"></a>Сведения о базе данных SQL Azure
Каждая база данных в базе данных SQL Azure связана с логическим сервером. База данных может:

- представлять собой отдельную базу данных с [собственным набором ресурсов](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU);
- входить в состав [пула баз данных](sql-database-elastic-pool.md), в котором [набор ресурсов используется совместно](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU);
- входить в состав [масштабируемого набора сегментированных баз данных](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), которые могут быть отдельными базами данных или частью пула;
- входить в состав набора баз данных, использующихся в [шаблоне разработки для мультитенантных приложений SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md), которые могут быть отдельными базами данных или быть частью пула (или этот набор может иметь смешанную конфигурацию). 

Дополнительные сведения о базах данных SQL Azure см. в [этой статье](sql-database-overview.md).

## <a name="what-features-are-supported"></a>Поддерживаемые функции

В следующих таблицах перечислены основные функции базы данных SQL Azure и SQL Server, представлены сведения о возможности их поддержки и указаны ссылки на дополнительные сведения об использовании функций на каждой платформе. Чтобы получить сведения о функциях Transact-SQL, перейдите по ссылке в таблице для категории функции. Дополнительные сведения о причинах отсутствия поддержки некоторых функций см. в статье [Отличия Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md).

Мы продолжаем расширять функционал версии&12;. Поэтому рекомендуем вам почаще посещать нашу веб-страницу с обновлениями служб Azure и при работе с ней пользоваться фильтрами.

* Фильтруйте обновления по [службе базы данных SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Фильтруйте обновления по [объявлениям об общедоступных версиях](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) функций базы данных SQL.

> [!TIP]
> Инструкции по тестированию баз данных на совместимость с базой данных SQL Azure см. в статье [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).
>

| **Компонент** | **SQL Server** | **База данных SQL Azure;** | 
| --- | :---: | :---: | 
| Активная георепликация | Не поддерживается. Ознакомьтесь со статьей [Группы доступности AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). | [Поддерживаются](sql-database-geo-replication-overview.md)
| Постоянное шифрование | [Поддерживаются](https://msdn.microsoft.com/library/mt163865.aspx) | [Поддерживаются](sql-database-always-encrypted.md) |
| Группы доступности AlwaysOn | [Поддерживаются](https://msdn.microsoft.com/library/hh510230.aspx) | Не поддерживается. Ознакомьтесь со статьей [Обзор: активная георепликация для базы данных SQL](sql-database-geo-replication-overview.md). |
| Присоединение базы данных | [Поддерживаются](https://msdn.microsoft.com/library/ms190209.aspx) | Не поддерживается |
| Роли приложений | [Поддерживаются](https://msdn.microsoft.com/library/ms190998.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ms190998.aspx) |
| Автомасштабирование | Не поддерживается | [Поддерживаются](sql-database-scale-up.md) |
| Azure Active Directory | Не поддерживается | [Поддерживаются](sql-database-aad-authentication.md) |
| Фабрика данных Azure | Не поддерживается. Ознакомьтесь со статьей [Службы SQL Server Integration Services](https://msdn.microsoft.com/library/ms141026.aspx). | [Поддерживаются](https://azure.microsoft.com/services/data-factory/) |
| Аудит | [Поддерживаются](https://msdn.microsoft.com/library/cc280386.aspx) | [Поддерживаются](sql-database-auditing-get-started.md) |
| BACPAC-файл (экспорт) | [Поддерживаются](https://msdn.microsoft.com/library/hh213241.aspx) | [Поддерживаются](sql-database-export.md) |
| BACPAC-файл (импорт) | [Поддерживаются](https://msdn.microsoft.com/library/hh710052.aspx) | [Поддерживаются](sql-database-import.md) |
| Инструкции BACKUP и RESTORE | [Поддерживаются](https://msdn.microsoft.com/library/ff848768.aspx) | Не поддерживается |
| Встроенные функции | [Поддерживаются](https://msdn.microsoft.com/library/ms174318.aspx) | [Большинство](https://msdn.microsoft.com/library/ms174318.aspx) |
| Запись измененных данных | [Поддерживаются](https://msdn.microsoft.com/library/cc645937.aspx) | Не поддерживается |
| Отслеживание изменений | [Поддерживаются](https://msdn.microsoft.com/library/bb933875.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/bb933875.aspx) |
| Инструкции параметров сортировки | [Поддерживаются](https://msdn.microsoft.com/library/ff848763.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ff848763.aspx) |
| Индексы columnstore | [Поддерживаются](https://msdn.microsoft.com/library/gg492088.aspx) | [Только в Premium Edition](https://msdn.microsoft.com/library/gg492088.aspx) |
| Среда CLR | [Поддерживаются](https://msdn.microsoft.com/library/ms131102.aspx) | Не поддерживается |
| автономные базы данных; | [Поддерживаются](https://msdn.microsoft.com/library/ff929071.aspx) | Встроены |
| Автономные пользователи | [Поддерживаются](https://msdn.microsoft.com/library/ff929188.aspx) | [Поддерживаются](sql-database-manage-logins.md#non-administrator-users) |
| Ключевые слова языка управления потоком | [Поддерживаются](https://msdn.microsoft.com/library/ms174290.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ms174290.aspx) |
| Запросы баз данных | [Поддерживаются](https://msdn.microsoft.com/library/dn584627.aspx) | [Эластичные запросы](sql-database-elastic-query-overview.md) |
| Курсоры | [Поддерживаются](https://msdn.microsoft.com/library/ms181441.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ms181441.aspx) | 
| Сжатие данных | [Поддерживаются](https://msdn.microsoft.com/library/cc280449.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/cc280449.aspx) |
| Резервные копии базы данных | [Доступны для пользователей](https://msdn.microsoft.com/library/ms187048.aspx) | [Встроены](sql-database-automated-backups.md) |
| Компонент Database Mail | [Поддерживаются](https://msdn.microsoft.com/library/ms189635.aspx) | Не поддерживается |
| Зеркальное отображение базы данных | [Поддерживаются](https://msdn.microsoft.com/library/ms189852.aspx) | Не поддерживается |
| Параметры конфигурации базы данных | [Поддерживаются](https://msdn.microsoft.com/library/mt629158.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services (DQS) | [Поддерживаются](https://msdn.microsoft.com/library/ff877925.aspx) | Не поддерживается |
| Моментальные снимки базы данных | [Поддерживаются](https://msdn.microsoft.com/library/ms175158.aspx) | Не поддерживается |
| Типы данных | [Поддерживаются](https://msdn.microsoft.com/library/ms187752.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ms187752.aspx) |  
| Инструкции DBCC | [Все](https://msdn.microsoft.com/library/ms188796.aspx) | [Некоторые](https://msdn.microsoft.com/library/ms188796.aspx) |
| Инструкции языка DDL | [Поддерживаются](https://msdn.microsoft.com/library/ff848799.aspx) | [Большинство](https://msdn.microsoft.com/library/ff848799.aspx)
| Триггеры DDL | [Поддерживаются](https://msdn.microsoft.com/library/ms175941.aspx) | [Только база данных](https://msdn.microsoft.com/library/ms175941.aspx) |
| Распределенные транзакции | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | Только сценарии базы данных SQL |
| Инструкции языка DML | [Поддерживаются](https://msdn.microsoft.com/library/ff848766.aspx) | [Большинство](https://msdn.microsoft.com/library/ff848766.aspx) |
| Триггеры DML | [Поддерживаются](https://msdn.microsoft.com/library/ms178110.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ms178110.aspx) |
| DMV | [Все](https://msdn.microsoft.com/library/ms188754.aspx) | [Некоторые](https://msdn.microsoft.com/library/ms188754.aspx) |
| эластичные пулы | Не поддерживается | [Поддерживаются](sql-database-elastic-pool.md) |
| Задания обработки эластичных БД | Не поддерживаются. Ознакомьтесь со статьей [SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx). | [Поддерживаются](sql-database-elastic-jobs-getting-started.md) | 
| Эластичные запросы | Не поддерживаются. Ознакомьтесь со статьей [Межбазовые запросы](https://msdn.microsoft.com/library/dn584627.aspx). | [Поддерживаются](sql-database-elastic-query-overview.md) |
| Уведомления о событиях | [Поддерживаются](https://msdn.microsoft.com/library/ms186376.aspx) | [Поддерживаются](sql-database-insights-alerts-portal.md) |
| Выражения | [Поддерживаются](https://msdn.microsoft.com/library/ms190286.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ms190286.aspx) |
| Расширенные события | [Поддерживаются](https://msdn.microsoft.com/library/bb630282.aspx) | [Некоторые](sql-database-xevent-db-diff-from-svr.md) |
| Расширенные хранимые процедуры | [Поддерживаются](https://msdn.microsoft.com/library/ms164627.aspx) | Не поддерживается |
| Группы файлов | [Поддерживаются](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [Только первичные](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| Файловый поток | [Поддерживаются](https://msdn.microsoft.com/library/gg471497.aspx) | Не поддерживается |
| полнотекстовый поиск. | [Поддерживаются](https://msdn.microsoft.com/library/ms142571.aspx) | [Не поддерживаются сторонние средства разбиения текста на слова](https://msdn.microsoft.com/library/ms142571.aspx) |
| Функции | [Поддерживаются](https://msdn.microsoft.com/library/ms174318.aspx) | [Большинство](https://msdn.microsoft.com/library/ms174318.aspx) |
| Оптимизация в памяти | [Поддерживаются](https://msdn.microsoft.com/library/dn133186.aspx) | [Только в Premium Edition](https://msdn.microsoft.com/library/dn133186.aspx) |
| Задания | [Агент SQL Server](https://msdn.microsoft.com/library/ms189237.aspx) | [Поддерживаются](sql-database-elastic-jobs-getting-started.md) |
| Поддержка данных JSON | [Поддерживаются](https://msdn.microsoft.com/library/dn921897.aspx) | [Поддерживаются](sql-database-json-features.md) |
| Элементы языка | [Поддерживаются](https://msdn.microsoft.com/library/ff848807.aspx) | [Большинство](https://msdn.microsoft.com/library/ff848807.aspx) |  
| Связанные службы | [Поддерживаются](https://msdn.microsoft.com/library/ms188279.aspx) | Не поддерживаются. Ознакомьтесь со статьей об [эластичных запросах](sql-database-elastic-query-horizontal-partitioning.md). |
| Доставка журналов | [Поддерживаются](https://msdn.microsoft.com/library/ms187103.aspx) | Не поддерживается. Ознакомьтесь со статьей [Обзор: активная георепликация для базы данных SQL](sql-database-geo-replication-overview.md). |
| Команды управления | [Поддерживаются](https://msdn.microsoft.com/library/ms190286.aspx)| [Не поддерживаются](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services (MDS) | [Поддерживаются](https://msdn.microsoft.com/library/ff487003.aspx) | Не поддерживается |
| Минимальное ведение журнала при массовом импорте | [Поддерживаются](https://msdn.microsoft.com/library/ms190422.aspx) | Не поддерживается |
| Изменение системных данных | [Поддерживаются](https://msdn.microsoft.com/library/ms178028.aspx) | Не поддерживается |
| Операции с индексом в сети | [Поддерживаются](https://msdn.microsoft.com/library/ms177442.aspx) | [Размер транзакции ограничен уровнем службы](https://msdn.microsoft.com/library/ms177442.aspx) |
| Операторы | [Поддерживаются](https://msdn.microsoft.com/library/ms174986.aspx) | [Большинство](https://msdn.microsoft.com/library/ms174986.aspx) |
| Восстановление базы данных до точки во времени | [Поддерживаются](https://msdn.microsoft.com/library/ms179451.aspx) | [Поддерживаются](sql-database-recovery-using-backups.md#point-in-time-restore) |
| PolyBase | [Поддерживаются](https://msdn.microsoft.com/library/mt143171.aspx) | [Не поддерживается]
| Управление на основе политик | [Поддерживаются](https://msdn.microsoft.com/library/bb510667.aspx) | Не поддерживается |
| Предикаты | [Поддерживаются](https://msdn.microsoft.com/library/ms189523.aspx) | [Большинство](https://msdn.microsoft.com/library/ms189523.aspx)
| Регулятор ресурсов | [Поддерживаются](https://msdn.microsoft.com/library/bb933866.aspx) | [Встроен](sql-database-service-tiers.md) |
| Восстановление базы данных из резервной копии | [Поддерживаются](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [Только из встроенных резервных копий](sql-database-recovery-using-backups.md) |
| Безопасность на уровне строк | [Поддерживаются](https://msdn.microsoft.com/library/dn765131.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/dn765131.aspx) |
| Инструкции по безопасности | [Поддерживаются](https://msdn.microsoft.com/library/ff848791.aspx) | [Некоторые](https://msdn.microsoft.com/library/ff848791.aspx) |
| Семантический поиск | [Поддерживаются](https://msdn.microsoft.com/library/gg492075.aspx) | Не поддерживается |
| Порядковые номера | [Поддерживаются](https://msdn.microsoft.com/library/ff878058.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ff878058.aspx) |
| Service Broker | [Поддерживаются](https://msdn.microsoft.com/library/bb522893.aspx) | Не поддерживается |
| Параметры конфигурации сервера | [Поддерживаются](https://msdn.microsoft.com/library/ms189631.aspx) | Не поддерживаются. Ознакомьтесь со статьей о [параметрах конфигурации базы данных](https://msdn.microsoft.com/library/mt629158.aspx). |
| Инструкции SET | [Поддерживаются](https://msdn.microsoft.com/library/ms190356.aspx) | [Большинство](https://msdn.microsoft.com/library/ms190356.aspx) 
| пространственный индекс | [Поддерживаются](https://msdn.microsoft.com/library/bb933790.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/bb933790.aspx) |
| Агент SQL Server | [Поддерживаются](https://msdn.microsoft.com/library/ms189237.aspx) | Не поддерживается. Ознакомьтесь со статьей о [заданиях обработки эластичных БД](sql-database-elastic-jobs-getting-started.md). |
| SQL Server Analysis Services (SSAS) | [Поддерживаются](https://msdn.microsoft.com/library/bb522607.aspx) | Не поддерживаются. Ознакомьтесь со страницей [Службы Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/). |
| SQL Server Integration Services (SSIS); | [Поддерживаются](https://msdn.microsoft.com/library/ms141026.aspx) | Не поддерживаются. Ознакомьтесь со страницей [Фабрика данных](https://azure.microsoft.com/services/data-factory/). |
| SQL Server PowerShell | [Поддерживаются](https://msdn.microsoft.com/library/hh245198.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/hh245198.aspx) |
| SQL Server Profiler | [Поддерживаются](https://msdn.microsoft.com/library/ms181091.aspx) | Не поддерживается. Ознакомьтесь со статьей о [расширенных событиях](https://msdn.microsoft.com/library/ms181091.aspx). |
| Репликация SQL Server | [Поддерживаются](https://msdn.microsoft.com/library/ms151198.aspx) | [Только для подписчиков репликации транзакций и репликации моментального снимка](sql-database-cloud-migrate-compatible-using-transactional-replication.md) |
| SQL Server Reporting Services (SSRS) | [Поддерживаются](https://msdn.microsoft.com/library/ms159106.aspx) | Не поддерживается |
| Хранимые процедуры | [Поддерживаются](https://msdn.microsoft.com/library/ms190782.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ms190782.aspx) |
| Системные хранимые функции | [Поддерживаются](https://msdn.microsoft.com/library/ff848780.aspx) | [Некоторые](https://msdn.microsoft.com/library/ff848780.aspx) |
| Системные хранимые процедуры | [Поддерживаются](https://msdn.microsoft.com/library/ms187961.aspx) | [Некоторые](https://msdn.microsoft.com/library/ms187961.aspx) |
| Системные таблицы | [Поддерживаются](https://msdn.microsoft.com/library/ms179932.aspx) | [Некоторые](https://msdn.microsoft.com/library/ms179932.aspx) |
| Системные представления | [Поддерживаются](https://msdn.microsoft.com/library/ms177862.aspx) | [Некоторые](https://msdn.microsoft.com/library/ms177862.aspx)
| Секционирование таблиц. | [Поддерживаются](https://msdn.microsoft.com/library/ms190787.aspx) | [Только первичная группа файлов](https://msdn.microsoft.com/library/ms190787.aspx) |
| Временные таблицы | [Локальные и глобальные](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [Только локальные](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| Временные таблицы | [Поддерживаются](https://msdn.microsoft.com/library/dn935015.aspx) | [Поддерживаются](sql-database-temporal-tables.md) |
| Инструкции транзакции | [Поддерживаются](https://msdn.microsoft.com/library/ms174377.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ms174377.aspx) |
| Переменные | [Поддерживаются](https://msdn.microsoft.com/library/ff848809.aspx) | | [Поддерживаются](https://msdn.microsoft.com/library/ff848809.aspx) | 
| Прозрачное шифрование данных (TDE)  | [Поддерживаются](https://msdn.microsoft.com/library/bb934049.aspx) | [Поддерживаются](https://msdn.microsoft.com/dn948096.aspx) |
| Отказоустойчивый кластер Windows Server | [Поддерживаются](https://msdn.microsoft.com/library/hh270278.aspx) | Не поддерживается. Ознакомьтесь со статьей [Обзор: активная георепликация для базы данных SQL](sql-database-geo-replication-overview.md). |
| XML-индексы | [Поддерживаются](http://msdn.microsoft.com/library/bb934097.aspx) | [Поддерживаются](http://msdn.microsoft.com/library/bb934097.aspx) |
| Инструкции XML | [Поддерживаются](https://msdn.microsoft.com/library/ff848798.aspx) | [Поддерживаются](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о службе базы данных SQL Azure см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md).
- Общие сведения о логических серверах Azure SQL см. в [этой статье](sql-database-server-overview.md).
- Общие сведения о базах данных Azure SQL см. в [этой статье](sql-database-overview.md).
- Сведения о поддержке функций Transact-SQL в базе данных SQL Azure см. в статье [Отличия Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md).
- Ознакомьтесь со сведениями о квотах и ограничениях для конкретных ресурсов с учетом вашего **уровня служб**. Общие сведения об уровнях служб см. в статье [Уровни служб базы данных SQL для отдельных баз данных и пулов эластичных баз данных](sql-database-service-tiers.md).
- Общие сведения о методах защиты в базе данных SQL см. в [этой статье](sql-database-security-overview.md).
- Сведения о доступности драйверов и поддержке для базы данных SQL см. в статье [Библиотеки подключений для базы данных SQL и SQL Server](sql-database-libraries.md).



<!--HONumber=Feb17_HO2-->


