---
title: "Ошибка при подключении к базе данных SQL: &quot;База данных на сервере сейчас недоступна&quot; | Документация Майкрософт"
description: "Устранение ошибки &quot;База данных на сервере сейчас недоступна&quot; при подключении приложения к базе данных SQL."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
keywords: "ошибка при подключении к базе данных SQL, база данных на сервере сейчас недоступна"
ms.assetid: f61999ac-d46b-448a-8830-3b04978d84ec
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ede7672196e71bc493cd9b758982445e991def1b


---
# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Ошибка при подключении к базе данных SQL: "База данных на сервере сейчас недоступна".
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Когда приложение подключается к базе данных SQL Azure, появляется следующее сообщение об ошибке:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Это сообщение об ошибке обычно временное (кратковременное).
> 
> 

Эта ошибка возникает, когда база данных Azure перемещается (или перенастраивается) и приложение потеряло подключение к базе данных SQL. События перенастройки базы данных SQL обычно происходят в запланированном случае (например при обновлении программного обеспечения) или в незапланированном случае (например при сбое процесса или балансировке нагрузки). Большей частью события перенастройки кратковременные и должны завершаться в течение 60 секунд максимум. Тем не менее завершение этих событий иногда может занимать больше времени, например когда большая транзакция приводит к длительному восстановлению.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Порядок устранения временных проблем подключения
1. Проверьте [панель мониторинга служб Microsoft Azure](https://azure.microsoft.com/status) на наличие каких-либо известных сбоев, произошедших в то время, когда приложение сообщало об ошибках.
2. Приложения, подключающиеся к облачной службе, такой как база данных SQL Azure, должны ожидать периодические события перенастройки и реализовывать логику повторов для обработки этих ошибок, а не отображать их как ошибки приложения для пользователей. Дополнительные сведения и общие стратегии повторов см. в разделе [Временные ошибки (временные сбои)](sql-database-connectivity-issues.md) и в статье [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md). Затем ознакомьтесь с примерами кода в [библиотеке подключений для базы данных SQL и SQL Server](sql-database-libraries.md).
3. Если база данных близка к исчерпанию доступных ресурсов, может возникать временная проблема подключения. См. статью [Советы по настройке производительности базы данных SQL](sql-database-troubleshoot-performance.md).
4. Если проблемы подключения остаются или интервал, во время которого приложение обнаруживает ошибку, превышает 60 секунд, а также если в определенный день такая ошибка возникает многократно, зарегистрируйте запрос на поддержку Azure, нажав **Получить поддержку** на сайте [Поддержка Azure](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Дальнейшие действия
* При появлении другой ошибки проанализируйте [сообщение об ошибке](sql-database-develop-error-messages.md) , чтобы разобраться в вызвавших ее причинах.
* Если проблема сохраняется, см. указания в статье [Устранение неполадок подключения к базе данных SQL Azure](sql-database-troubleshoot-common-connection-issues.md).




<!--HONumber=Nov16_HO3-->


