---
title: "Устранение распространенных неполадок подключения к базе данных SQL Azure"
description: "Порядок обнаружения и устранения распространенных ошибок подключения в базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: c033c1b32ad7b69565f870636110d317d01266df
ms.openlocfilehash: 28489985797a638c04e1fabba30f42dac56d4d9c


---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Устранение неполадок подключения к базе данных SQL Azure
При сбоях подключения к базе данных SQL Azure отображаются [сообщения об ошибке](sql-database-develop-error-messages.md). Эта статья представляет собой объединенный раздел, который поможет в устранении неполадок подключения к базе данных SQL Azure. В ней описываются [распространенные причины](#cause) проблем подключения, рекомендуется [инструмент устранения неполадок](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues), который поможет выявить проблему, и приводятся пошаговые инструкции по устранению [временных ошибок](#troubleshoot-transient-errors) и [постоянных или повторяющихся ошибок](#troubleshoot-the-persistent-errors). Наконец, в ней перечислены [все соответствующие статьи о проблемах подключения к базе данных SQL Azure](#all-topics-for-azure-sql-database-connection-problems).

Если у вас возникли проблемы с подключением, попробуйте устранить неполадки с помощью описанных в этой статье методов.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Причина:
Проблемы подключения могут быть вызваны любой из следующих причин.

* В процессе разработки приложения не были соблюдены рекомендации и указания по разработке.  Чтобы начать, см. статью [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md).
* Перенастройка базы данных SQL Azure.
* Параметры брандмауэра
* Время ожидания подключения.
* Неправильные сведения для входа.
* Достигнуто максимальное ограничение для некоторых ресурсов базы данных SQL Azure.

В общих чертах проблемы подключения к базе данных SQL Azure можно классифицировать следующим образом:

* [временные ошибки (кратковременные или возникающие периодически);](#troubleshoot-transient-errors)
* [постоянные или регулярно возникающие ошибки.](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Применение средства устранения неполадок для диагностики проблем подключения к базе данных SQL Azure
При возникновении определенной ошибки подключения попробуйте [этот инструмент](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), который поможет быстро выявить и устранить проблему.

## <a name="troubleshoot-transient-errors"></a>Устранение временных ошибок
Если в приложении возникают временные ошибки, обратитесь к следующим разделам, чтобы получить советы по их устранению или сокращению частоты их возникновения:

* [Ошибка при подключении к базе данных SQL: "База данных на сервере сейчас недоступна"](sql-database-troubleshoot-connection.md)
* [Устранение, диагностика и предотвращение ошибок подключения SQL и временных ошибок для базы данных SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Устранение постоянных (повторяющихся) ошибок
Если при подключении к базе данных SQL Azure в приложении постоянно возникают сбои, обычно причины бывают следующими:

* Конфигурация брандмауэра. База данных SQL Azure или брандмауэр на стороне клиента блокирует подключения к базе данных SQL Azure.
* Перенастройка сети на стороне клиента, например новый IP-адрес или прокси-сервер.
* Ошибка пользователя, например неправильно введенные параметры подключения, такие как имя сервера в строке подключения.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Порядок устранения постоянных проблем подключения
1. Настройте [правила брандмауэра](sql-database-configure-firewall-settings.md) , разрешив предоставление IP-адреса клиенту. Для временных целей тестирования настройте правило брандмауэра, используя 0.0.0.0 как начало диапазона IP-адресов и 255.255.255.255 как завершение диапазона IP-адресов. Откроется сервер для всех IP-адресов. Если это позволяет устранить проблемы с подключением, удалите это правило и создайте правило брандмауэра для надлежащим образом лимитированных IP-адресов или их диапазона. 
2. На всех брандмауэрах между клиентом и Интернетом откройте порт 1433 для входящих соединений. См инструкции по настройке [брандмауэра Windows для разрешения доступа к SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) и [портов и протоколов, необходимых для гибридной идентификации](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports). Вы узнаете о дополнительных указателях, относящихся к дополнительным портам, которые необходимо открыть для аутентификации в Azure Active Directory.
3. Проверьте строку подключения и другие параметры подключения. Ознакомьтесь с разделом "Строка подключения" в [статье о проблемах подключения](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4. Проверьте работоспособность службы на панели мониторинга. Если вы считаете, что имеет место региональный сбой, выполните инструкции по восстановлению в новый регион из раздела [Восстановление после сбоя](sql-database-disaster-recovery.md) .

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Все разделы о проблемах подключения к Базе данных SQL Azure
В следующей таблице перечислены разделы о проблемах подключения, относящихся непосредственно к службе Базы данных SQL Azure.

| &nbsp; | Название | Описание |
| ---:|:--- |:--- |
| 1 |[Устранение неполадок подключения к базе данных SQL Azure](sql-database-troubleshoot-common-connection-issues.md) |Это целевая страница для устранения проблем подключения в базе данных SQL Azure. На ней описывается, как выявлять и устранять временные и постоянные или повторяющиеся ошибки в базе данных SQL Azure. |
| 2 |[Устранение, диагностика и предотвращение ошибок подключения SQL и временных ошибок для базы данных SQL](sql-database-connectivity-issues.md) |Узнайте, как устранять, диагностировать и предотвращать ошибки подключения SQL или временные ошибки в базе данных SQL Azure. |
| 3 |[Общие рекомендации по повторным попыткам](../best-practices-retry-general.md) |Содержит общие рекомендации по обработке временных сбоев при подключении к базе данных SQL Azure. |
| 4. |[Устранение неполадок подключения к Базе данных SQL Microsoft Azure](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) |Этот инструмент помогает выявить проблему и устранить ошибки подключения. |
| 5 |[Ошибка при подключении к базе данных SQL: "База данных на сервере сейчас недоступна". Повторите попытку подключения позже"](sql-database-troubleshoot-connection.md) |Описывает, как определить и устранить ошибку 40613: "База данных &lt;x&gt; на сервере &lt;y&gt; сейчас недоступна. Повторите попытку подключения позже". |
| 6 |[Коды ошибок SQL для клиентских приложений базы данных SQL: ошибки подключения к базе данных и другие проблемы](sql-database-develop-error-messages.md) |Содержит сведения о кодах ошибок SQL для клиентских приложений базы данных SQL, например общих ошибок подключения к базе данных, проблем при копировании базы данных и общих ошибок. |
| 7 |[Руководство по производительности базы данных SQL Azure](sql-database-performance-guidance.md) |Содержит указания по выбору уровня служб, который подходит для вашего приложения. Кроме того, содержит рекомендации по настройке приложения для наиболее эффективного использования базы данных SQL Azure. |
| 8 |[Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md) |Содержит ссылки на примеры кода для различных технологий, которые можно использовать для подключения к базе данных SQL Azure и взаимодействия с ней. |

## <a name="next-steps"></a>Дальнейшие действия
* [Устранение проблем производительности базы данных SQL Azure](sql-database-troubleshoot-performance.md)
* [Устранение проблем с разрешениями в базе данных SQL Azure](sql-database-troubleshoot-permissions.md)
* [Поиск документации по Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Просмотр последних обновлений для службы базы данных SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Общие сведения о разработке базы данных SQL](sql-database-develop-overview.md)
* [Общие рекомендации по повторным попыткам](../best-practices-retry-general.md)
* [Библиотеки подключений для Базы данных SQL и SQL Server](sql-database-libraries.md)
* [Схема обучения использованию базы данных SQL Azure](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
* [Схема обучения использованию функций и инструментов эластичной базы данных](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 




<!--HONumber=Feb17_HO1-->


