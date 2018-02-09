---
title: "Как настроить параметры сервера в базе данных Azure для MySQL | Документация Майкрософт"
description: "В этой статье описывается, как настроить параметры MySQL сервера в базе данных Azure для MySQL с помощью портала Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 59eeed42356a276c259bd8da55890b7ada67d729
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Как настроить параметры сервера в базе данных Azure для MySQL с помощью портала Azure

База данных Azure для MySQL поддерживает настройку некоторых параметров сервера. В этой статье описывается настройка этих параметров с помощью портала Azure. Не все параметры сервера можно настроить. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Переход к колонке параметров сервера на портале Azure
1. Войдите на портал Azure, а затем найдите свою базу данных Azure для MySQL.
2. В разделе **Параметры** щелкните **Параметры сервера**, чтобы открыть страницу параметров сервера для базы данных Azure для MySQL.
3. Найдите все параметры, которые необходимо настроить. Просмотрите столбец **Описание**, чтобы понять назначение и допустимые значения. 
4. Нажмите кнопку **Сохранить**, чтобы сохранить изменения.

![Страница параметров сервера на портале Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Список доступных для настройки параметров сервера

Список поддерживаемых параметров постоянно растет. Вы можете использовать вкладку параметров сервера на портале Azure, чтобы получить определение и настроить параметры сервера на основе требований своего приложения. 

## <a name="nonconfigurable-server-parameters"></a>Ненастраиваемые параметры сервера
Буферный пул InnoDB и максимальное число подключений не подлежат настройке и привязаны к [ценовой категории](concepts-service-tiers.md). 

| **Ценовая категория** | **Буферный пул InnoDB** | **Максимальное число подключений** |
| :------------------------ | :-------- | :----------- |
| Базовый, 50 | 1024 | 50 | 
| Базовый, 100  | 2560 | 100 | 
| Стандартный, 100 | 2560 | 200 | 
| Стандартный, 200 | 5120 | 400 | 
| Стандартный, 400 | 10240 | 800 | 
| Стандартный, 800 | 20480 | 1600 |

Эти дополнительные параметры сервера нельзя настроить в системе:

|**Параметр**|**Фиксированное значение**|
| :------------------------ | :-------- |
|innodb_file_per_table для уровня "Базовый"|ВЫКЛ.|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 МБ|

Для остальных параметров сервера, которые не перечислены выше, устанавливаются значения MySQL по умолчанию для версий [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) и [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>Дополнительная информация
- [Библиотеки подключений для базы данных Azure для MySQL](concepts-connection-libraries.md).
