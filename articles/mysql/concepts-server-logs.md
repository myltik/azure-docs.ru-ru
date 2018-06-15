---
title: Журналы сервера в службе "База данных Azure для MySQL"
description: Описание журналов, доступных в базе данных Azure для MySQL, и параметров для управления уровнями ведения журнала.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 50e4b9b8b8f9433ec725aaa982e969cec7afb91c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265791"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Журналы сервера в базе данных Azure для MySQL
В базе данных Azure для MySQL пользователям доступен журнал медленных запросов. Доступ к журналам транзакций не поддерживается. Журнал медленных запросов можно использовать для выявления проблем с производительностью при устранении неполадок. 

Дополнительные сведения о журнале медленных запросов MySQL см. в [этом разделе справочного руководства по MySQL](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Доступ к журналам сервера
Чтобы просмотреть и скачать журналы сервера в базе данных Azure для MySQL, можно воспользоваться порталом Azure или Azure CLI.

На портале Azure выберите нужный сервер базы данных Azure для MySQL. В разделе **Мониторинг** найдите страницу **Журналы сервера**.

Описание работы с Azure CLI см. в статье [Настройка журналов сервера и получение к ним доступа с помощью Azure CLI](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Хранение журналов
Журналы доступны в течение семи дней с момента создания. Если общий объем доступных журналов превышает 7,5 ГБ, по мере необходимости удаляются самые старые файлы. 

Новый файл журнала создается каждые 24 часа или при достижении размера файла 7,5 ГБ.


## <a name="configure-logging"></a>Настройка журнала 
Журнал медленных запросов по умолчанию отключен. Чтобы включить его, установите для параметра slow_query_log значение "ON" (Включено).

Вы можете настроить еще несколько параметров.

- **long_query_time.** Если запрос занимает больше времени, чем задано значением long_query_time (в секундах), информация о нем заносится в журнал. По умолчанию это 10 секунд.
- **log_slow_admin_statements.** Указывает, нужно ли сохранять в журнал slow_query_log административные инструкции, например ALTER_TABLE и ANALYZE_TABLE.
- **log_queries_not_using_indexes**. Указывает, нужно ли сохранять в журнал slow_query_log запросы, не использующие индексы.
- **log_throttle_queries_not_using_indexes.** Ограничивает число не использующих индексы запросов, сохраняемых в журнале медленных запросов. Этот параметр применяется, только если log_queries_not_using_indexes имеет значение "ON" (Включено).

Полное описание параметров, применимых для журнала медленных запросов, вы найдете в [соответствующем разделе документации по MySQL](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="next-steps"></a>Дальнейшие действия
- [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md) (Настройка и использование журналов сервера с помощью Azure CLI)
