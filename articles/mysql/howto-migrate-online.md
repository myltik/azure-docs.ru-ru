---
title: "Минимальное время простоя миграции к базе данных Azure для MySQL | Документы Microsoft"
description: "В этой статье описывается, как выполнить миграцию базы данных MySQL к базе данных Azure минимальным временем простоя для MySQL и настройке начальной загрузки и синхронизации непрерывные данные из базы данных-источника в целевую базу данных с помощью Attunity Replicate для Microsoft Миграция."
services: mysql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 429f5c9f939a802184a6513a63fb9115abf4b235
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Минимальное время простоя миграции к базе данных Azure для MySQL
Можно перенести существующую базу данных MySQL к базе данных Azure для MySQL с помощью Attunity Replicate для Microsoft миграций, предложение совместно бесплатный, отсчитываемые от Attunity и корпорацией Майкрософт, которая предоставляется вместе с служба миграции базы данных Azure не Дополнительные затраты клиентам корпорации Майкрософт. Attunity Replicate для Microsoft миграций также включает минимальное время простоя в миграции базы данных, и база данных-источник продолжает работать во время процесса миграции.

Attunity Replicate является средство репликации данных, который позволяет синхронизировать данные между разнообразных источников и целевых объектов, распространение скрипта создания схемы и данные, связанные с каждой таблицы базы данных. Attunity Replicate не распространяет других артефактов (например, SP, триггеры, функции, т. д.) или convert, например, PL/SQL-кода, размещенного в таким артефактам, T-SQL.

> [!NOTE]
> Хотя Attunity Replicate поддерживает широкий набор сценариев миграции, Attunity Replicate для Microsoft миграций предназначен для поддержки для подмножества пары исходной или целевой.

Содержит обзор процесса миграции минимальным временем простоя.

1. **Миграция в исходной схеме MySQL** для базы данных Azure для базы данных MySQL с помощью [MySQL Workbench](https://www.mysql.com/products/workbench/).

2. **Настройка начальной загрузки и синхронизации непрерывные данные из базы данных-источника в целевую базу данных** с помощью Attunity Replicate для Microsoft миграций. Это сводит к минимуму время, которые должны быть установлены базы данных-источника, как только для чтения в процессе подготовки для переключения приложения в целевую базу данных MySQL в Azure.

Дополнительные сведения о Attunity Replicate для миграций Microsoft предложения просмотрите следующие ресурсы:
 - Attunity Replicate для Microsoft миграций [веб-страницы](https://aka.ms/attunity-replicate).
 - [Загрузить](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity Replicate для Microsoft миграций.
 - Attunity Replicate [сообщества](https://microsoft.attunity.com/)с кратком руководстве, учебники и поддержки.
 - Пошаговое руководство по использованию Attunity для миграции от MySQL Azure для MySQL см. в разделе [руководство по миграции базы данных](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).