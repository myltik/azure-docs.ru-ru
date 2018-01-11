---
title: "Минимальное время простоя миграции к базе данных Azure для PostgreSQL | Документы Microsoft"
description: "В этой статье описывается, как выполнить миграцию минимальным временем простоя, извлечение данных PostgreSQL в файл дампа, восстановление базы данных PostgreSQL из файла архива, созданного по pg_dump в базе данных Azure для PostgreSQL и настройке начальной загрузки и непрерывные данные синхронизации из базы данных-источника в целевую базу данных с помощью Attunity Replicate для Microsoft миграций."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 8c98c58042e7f1d1726eaad6ee03d1531b6c910e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Минимальное время простоя миграции к базе данных Azure для PostgreSQL
Можно перенести существующей базы данных PostgreSQL к базе данных Azure для PostgreSQL, используя Attunity Replicate для Microsoft миграций, предложение совместно бесплатный, отсчитываемые от Attunity и корпорацией Майкрософт, которая предоставляется вместе с службу миграции базы данных Azure для клиентов Microsoft без дополнительных затрат. Attunity Replicate для Microsoft миграций также включает минимальное время простоя в миграции базы данных, и база данных-источник продолжает работать во время процесса миграции.

Attunity Replicate является средство репликации данных, который позволяет синхронизировать данные между разнообразных источников и целевых объектов, распространение скрипта создания схемы и данные, связанные с каждой таблицы базы данных. Attunity Replicate не распространяет других артефактов (например, SP, триггеры, функции, т. д.) или convert, например, PL/SQL-кода, размещенного в таким артефактам, T-SQL.

> [!NOTE]
> Хотя Attunity Replicate поддерживает широкий набор сценариев миграции, Attunity Replicate для Microsoft миграций предназначен для поддержки для подмножества пары исходной или целевой.

Содержит обзор процесса миграции минимальным временем простоя.

1. **Миграция в исходной схеме PostgreSQL** для базы данных Azure для базы данных PostgreSQL с помощью [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) команду с параметром - n, а затем с помощью [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) команды.

2. **Настройка начальной загрузки и синхронизации непрерывные данные из базы данных-источника в целевую базу данных** с помощью Attunity Replicate для Microsoft миграций. Это уменьшает время, которые базы данных-источника, которые должны быть установлены только для чтения в процессе подготовки для переключения приложения в целевой базе данных PostgreSQL в Azure.

Дополнительные сведения о Attunity Replicate для миграций Microsoft предложения просмотрите следующие ресурсы:
 - Attunity Replicate для Microsoft миграций [веб-страницы](https://aka.ms/attunity-replicate).
 - [Загрузить](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity Replicate для Microsoft миграций.
 - Attunity Replicate [сообщества](https://microsoft.attunity.com/)с кратком руководстве, учебники и поддержки.
 - Пошаговое руководство по использованию Attunity для миграции из PostgreSQL базы данных Azure для PostgreSQL сослаться [руководство по миграции базы данных](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).