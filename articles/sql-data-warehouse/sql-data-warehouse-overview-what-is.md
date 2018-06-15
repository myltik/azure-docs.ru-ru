---
title: Что такое хранилище данных SQL Azure? | Документация Майкрософт
description: Распределенная база данных корпоративного класса, способная обрабатывать петабайты реляционных и нереляционных данных. Это первое облачное хранилище в отрасли, в котором можно увеличивать и уменьшать количество вычислительных ресурсов, а также приостанавливать их работу в считанные секунды.
services: sql-data-warehouse
author: igorstanko
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: overview
ms.component: design
ms.date: 04/17/2018
ms.author: igorstan
ms.reviewer: igorstan
ms.openlocfilehash: c8e6af4eabc96845ff6c237735cdb0da2ed24743
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185983"
---
# <a name="what-is-azure-sql-data-warehouse"></a>Что такое хранилище данных SQL Azure?

Хранилище данных SQL — это облачное корпоративное хранилище данных (EDW), использующее массовую параллельную обработку (MPP) для быстрого выполнения сложных запросов на несколько петабайт данных. Используйте хранилище данных SQL как ключевой компонент решения для больших данных. Импортируйте большие данные в хранилище данных SQL с помощью простых запросов T-SQL PolyBase и используйте возможности MPP для выполнения высокопроизводительной аналитики. Во время интеграции и анализа хранилище данных станет единственным оптимальным и надежным вариантом получения аналитических сведений для вашей компании.  


## <a name="key-component-of-big-data-solution"></a>Ключевой компонент решения для больших данных
Хранилище данных SQL — это ключевой компонент комплексного решения для больших данных в облаке.

![Решение хранилища данных](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

В облачном решении для работы с данными данные принимаются в хранилища больших данных из различных источников. После отправки в хранилище больших данных алгоритмы Hadoop, Spark и машинного обучения выполняют подготовку и обучение данных. Когда данные будут готовы к сложному анализу, хранилище данных SQL запрашивает хранилища больших данных с помощью PolyBase. PolyBase использует стандартные запросы T-SQL для передачи данных в хранилище данных SQL.
 
Хранилище данных SQL сохраняет данные в реляционных таблицах с хранением по столбцам. Этот формат значительно снижает затраты на хранение данных и повышает производительность запросов. После сохранения данных в хранилище данных SQL вы сможете выполнять аналитику в большом масштабе. По сравнению с традиционными системами баз данных запросы анализа выполняются за секунды вместо минут или за часы вместо дней. 

Результаты анализа можно передать в базы данных и приложения отчетности по всему миру. Специалисты по бизнес-аналитике могут получить данные для принятия обоснованных бизнес-решений.


## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вам уже известны некоторые сведения о хранилище данных SQL, узнайте о том, как [создать хранилище данных SQL][create a SQL Data Warehouse] и [загрузить демонстрационные данные][load sample data]. Если вы раньше не работали с Azure, используйте [глоссарий Azure][Azure glossary], чтобы узнать значение новых терминов. Или ознакомьтесь со следующими ресурсами, посвященными хранилищу данных SQL.  

* [Истории успеха клиентов]
* [Блоги]
* [Запросы функций]
* [Видеоролики]
* [Блоги группы консультирования клиентов]
* [Создание запроса в службу поддержки]
* [Форум MSDN]
* [Форум Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Создание запроса в службу поддержки]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Истории успеха клиентов]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Блоги]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Блоги группы консультирования клиентов]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Запросы функций]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Форум MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Форум Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Видеоролики]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
