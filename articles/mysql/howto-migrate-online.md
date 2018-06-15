---
title: Миграция в службу "База данных Azure для MySQL" с минимальным простоем
description: Из этой статьи вы узнаете, как перенести базу данных MySQL в службу "База данных Azure для MySQL" с минимальным простоем и настроить начальную загрузку данных из базы данных-источника в целевую базу данных, а также их непрерывную синхронизацию с помощью Attunity Replicate for Microsoft Migrations.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 99add55188615debdc96b6cfc8b21e34552fd9d4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267260"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Миграция в службу "База данных Azure для MySQL" с минимальным простоем
Вы можете перенести существующую базу данных MySQL в службу "База данных Azure для MySQL" с помощью Attunity Replicate for Microsoft Migrations. Attunity Replicate — это совместное предложение от компании Attunity и корпорации Майкрософт. Клиентам Майкрософт это предложение предоставляется вместе со службой Azure Database Migration Service без дополнительной платы. 

Attunity Replicate позволяет сократить до минимума время простоя при переносе баз данных и поддерживает работу базы данных-источника на протяжении всего процесса.

Attunity Replicate — это средство репликации данных, которое позволяет синхронизировать данные между разными источниками и целевыми объектами. Оно передает скрипт создания схемы и данные, связанные с каждой таблицей базы данных. Attunity Replicate не передает другие артефакты (например, SP, триггеры, функции и т. д.) и не преобразует, например, код PL/SQL, который размещается в этих артефактах, в T-SQL.

> [!NOTE]
> Хотя средство Attunity Replicate поддерживает широкий набор сценариев миграции, оно предназначено для поддержки определенного набора пар "источник — целевой объект".

Процесс миграции с минимальным простоем включает следующие основные этапы:

* **Миграция исходной схемы MySQL** в службу управляемой базы данных ("База данных Azure для MySQL") с помощью [MySQL Workbench](https://www.mysql.com/products/workbench/).

* **Настройка начальной загрузки данных из базы данных-источника в целевую базу данных и их непрерывной синхронизации** с помощью Attunity Replicate for Microsoft Migrations. Такая настройка позволяет сократить время, в течение которого база данных-источник работает в режиме только для чтения, пока вы готовитесь к переносу приложений в целевую базу данных MySQL в Azure.

Дополнительные сведения о предложении Attunity Replicate for Microsoft Migrations см. в следующих материалах:
 - Откройте страницу службы [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate).
 - Скачайте [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Чтобы получить краткие инструкции по началу работы, руководства и поддержку, посетите [сообщество Attunity Replicate](https://aka.ms/attunity-community).
 - Пошаговые инструкции по использованию Attunity Replicate для переноса существующей базы данных MySQL в службу "База данных Azure для MySQL" см. в [руководстве по переносу баз данных](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).