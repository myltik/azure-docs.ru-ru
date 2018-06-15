---
title: Миграция в базу данных Azure для PostgreSQL с минимальным временем простоя
description: В этой статье объясняется, как выполнить миграцию с минимальным временем простоя, используя извлечение данных PostgreSQL в файл дампа, восстановление базы данных PostgreSQL из файла архива, созданного с помощью команды pg_dump, в базе данных Azure для PostgreSQL, а также настройку начальной загрузки и непрерывную синхронизацию данных из базы данных-источника в целевую базу данных с помощью Attunity Replicate for Microsoft Migrations.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 48cf460405ae3985553f9bff29f4fd7abb008196
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2018
ms.locfileid: "29692095"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Миграция в базу данных Azure для PostgreSQL с минимальным временем простоя
Вы можете перенести существующую базу данных PostgreSQL в Базу данных Azure для PostgreSQL с помощью Attunity Replicate for Microsoft Migrations. Attunity Replicate — это совместное предложение от компании Attunity и корпорации Майкрософт. Клиентам Майкрософт это предложение предоставляется вместе со службой Azure Database Migration Service без дополнительной платы. 

Attunity Replicate позволяет сократить до минимума время простоя при переносе баз данных и поддерживает работу базы данных-источника на протяжении всего процесса.

Attunity Replicate — это средство репликации данных, которое позволяет синхронизировать данные между различными источниками и целевыми объектами. Оно передает скрипт создания схемы и данные, связанные с каждой таблицей базы данных. Attunity Replicate не передает другие артефакты (например, SP, триггеры, функции и т. д.) и не преобразует, например, код PL/SQL, который размещается в этих артефактах, в T-SQL.

> [!NOTE]
> Хотя средство Attunity Replicate поддерживает широкий набор сценариев миграции, оно предназначено для поддержки определенного набора пар "источник — целевой объект".

В общих чертах процесс миграции с минимальным временем простоя включает:

* **Перенос исходной схемы PostgreSQL** в Базу данных Azure для PostgreSQL с помощью команды [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) с параметром -n, а также с помощью команды [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html).

* **Настройка начальной загрузки данных из базы данных-источника в целевую базу данных и их непрерывной синхронизации** с помощью Attunity Replicate for Microsoft Migrations. Это позволяет сократить время на настройку базы данных-источника только для чтения, так как вы готовитесь перенести свои приложения в целевую базу данных PostgreSQL в Azure.

Дополнительные сведения о предложении Attunity Replicate for Microsoft Migrations см. в следующих материалах:
 - Откройте страницу службы [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate).
 - Скачайте [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Чтобы получить краткие инструкции по началу работы, руководства и поддержку, посетите [сообщество Attunity Replicate](https://aka.ms/attunity-community/).
 - Пошаговые инструкции по использованию Attunity Replicate для миграции из PostgreSQL в Базу данных Azure для PostgreSQL см. в [руководстве по переносу базы данных](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).