---
title: "Аудит базы данных SQL Azure | Документация Майкрософт"
description: "Аудит базы данных SQL Azure позволяет отслеживать события базы данных и записывать их в журнал аудита в учетной записи хранения Azure."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 8c9da10e6ebc67c464bf7b5e569cb2113a082d0c
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-auditing-concepts"></a>Основные понятия аудита базы данных SQL
Аудит базы данных SQL Azure позволяет отслеживать события базы данных и записывать их в журнал аудита в учетной записи хранения Azure.

Аудит может помочь вам соблюсти требования нормативов, проанализировать работу с базой данных и получить представление о расхождениях и аномалиях, которые могут указывать на бизнес-проблемы или предполагаемые нарушения безопасности.

Средства аудита способствуют соблюдению стандартов соответствия, но не гарантируют их выполнение. Дополнительную информацию о программах Azure, поддерживающих проверку соблюдения стандартов, см. в [Центре управления безопасностью Azure](https://azure.microsoft.com/support/trust-center/compliance/).

* [Обзор аудита баз данных SQL Azure]
* [Настройка аудита базы данных]
* [Анализ журналов и отчетов аудита]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Обзор аудита баз данных SQL Azure
Аудит базы данных SQL позволяет:

* **Сохранить** журнал аудита выбранных событий. Вы можете указать, какие категории действий базы данных должны проходить аудит.
* **Создавать отчеты** по действиям, производимым с базой данных. Вы можете использовать предварительно настроенные отчеты и панель мониторинга для быстрого начала работы с отчетностью по действиям и событиям.
* **Анализировать** отчеты. Вы можете искать подозрительные события, необычную деятельность и тенденции.

Существует два **метода аудита**:

* **Аудит больших двоичных объектов**: журналы записываются в хранилище BLOB-объектов Azure. Это сравнительно новый метод аудита, который обеспечивает более высокую производительность, поддерживает более высокую детализацию аудита на уровне объекта и является более экономически эффективным.
* **Аудит таблиц**: журналы записываются в хранилище таблиц Azure.

> [!IMPORTANT]
> Из-за нового аудита больших двоичных объектов база данных иначе наследует политику аудита сервера. 

Аудит можно настроить для разных категорий событий.

* Сведения о настройке аудита и управлении им с помощью портала Azure см. в разделе [Настройка аудита базы данных SQL и управление им на портале Azure](sql-database-auditing-portal.md).
* Сведения о настройке аудита и управлении им с помощью PowerShell см. в разделе [Настройка аудита базы данных SQL и управление им с помощью PowerShell](sql-database-auditing-powershell.md).
* Сведения о настройке аудита и управлении им с помощью REST API см. в разделе [Настройка аудита базы данных SQL и управление им с помощью REST API](sql-database-auditing-rest.md).

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

Политику аудита можно определить для конкретной базы данных или как политику сервера по умолчанию. Политика аудита сервера по умолчанию применяется ко всем существующим и вновь создаваемым базам данных на сервере.

## <a name="blob-auditing"></a>Аудит больших двоичных объектов

Если для сервера включен аудит больших двоичных объектов, он всегда применяется к базе данных (т. е. выполняется аудит всех баз данных на сервере). Это поведение не зависит от следующих параметров:
    - настройки аудита для базы данных;
    - положение флажка "Наследовать настройки от сервера" в колонке базы данных.

> [!IMPORTANT]
> Если аудит больших двоичных объектов включен для сервера, то его включение для базы данных **не** приводит к переопределению или изменению настроек аудита больших двоичных объектов, установленных для сервера. Оба аудита будут выполняться независимо друг от друга. Таким образом, аудит базы данных будет выполняться дважды: один раз в соответствии с политикой сервера и еще раз в соответствии с политикой базы данных.

Не устанавливайте политику аудита больших двоичных объектов одновременно для сервера и базы данных, за исключением следующих случаев.
 * Если для определенной базы данных нужно использовать другую *учетную запись хранения* или другой *срок хранения*.
 * Если для определенной базы данных вам нужен аудит типов или категорий событий, отличных от параметров аудита для остальных баз данных на этом сервере (например, если аудит вставки данных будет выполняться только для одной конкретной базы данных).

Во всех остальных случаях мы рекомендуем включать аудит больших двоичных объектов только на уровне сервера, а на уровне баз данных отключить все параметры аудита для всех баз данных.

## <a name="table-auditing"></a>Аудит таблиц

Если аудит таблиц включен на уровне сервера, он применяется только к тем базам данных, для которых в колонке базы данных установлен флажок "Наследовать настройки от сервера" (он установлен по умолчанию для всех существующих и создаваемых баз данных).

- Если этот флажок *установлен*, любые настройки аудита, установленные для базы данных, **переопределяют** параметры, установленные для этой базы данных на уровне сервера.

- Если флажок *снят*, а аудит на уровне базы данных отключен, для этой базы данных аудит выполняться не будет.

## <a name="analyze-audit-logs-and-reports"></a>Анализ журналов и отчетов аудита
Журналы аудита объединяются в учетной записи хранения Azure, выбранной на этапе настройки.

Журналы аудита можно просматривать с помощью таких инструментов, как [обозреватель хранилищ Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о настройке аудита и управлении им с помощью портала Azure см. в разделе [Настройка аудита базы данных SQL и управление им на портале Azure](sql-database-auditing-portal.md).
* Сведения о настройке аудита и управлении им с помощью PowerShell см. в разделе [Настройка аудита базы данных SQL и управление им с помощью PowerShell](sql-database-auditing-powershell.md).
* Сведения о настройке аудита и управлении им с помощью REST API см. в разделе [Настройка аудита базы данных SQL и управление им с помощью REST API](sql-database-auditing-rest.md).


<!--Anchors-->
[Обзор аудита баз данных SQL Azure]: #subheading-1
[Настройка аудита базы данных]: #subheading-2
[Анализ журналов и отчетов аудита]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

