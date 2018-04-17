---
title: Поддерживаемые версии в службе "База данных Azure для MySQL"
description: Описываются поддерживаемые версии в базе данных Azure для MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: 53c8d51ddf9b7465a99b8b0685d7f6ce177fc526
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Поддерживаемые версии сервера базы данных Azure для MySQL
Служба "База данных Azure для MySQL" разработана на базе [MySQL Community Edition](https://www.mysql.com/products/community/) с использованием подсистемы InnoDB.  Сейчас база данных Azure для MySQL поддерживает перечисленные ниже версии.

## <a name="mysql-version-5638"></a>MySQL версии 5.6.38
Сведения об улучшениях и исправлениях в MySQL 5.6.38 см. в [документации](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html) по MySQL.

## <a name="mysql-version-5720"></a>MySQL версии 5.7.20
Сведения об улучшениях и исправлениях в MySQL 5.7.20 см. в [документации](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.html) по MySQL.

> [!NOTE]
> В службе для перенаправления подключений к экземплярам сервера используется шлюз. После установки подключения в клиенте MySQL отображается версия MySQL, установленная в шлюзе, а не фактическая версия, которая работает на вашем экземпляре сервера MySQL. Чтобы определить версию MySQL на экземпляре сервера, выполните команду `SELECT VERSION();` в командной строке MySQL. 

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
Служба автоматически управляет установкой исправлений для обновления дополнительного номера версии. Обновление основных номеров версий не поддерживается (например, обновление MySQL 5.6 до MySQL 5.7).

## <a name="next-steps"></a>Дополнительная информация

Сведения о квотах и ограничениях для конкретных ресурсов с учетом вашего **уровня служб** представлены в статье [Уровни служб в базе данных Azure для MySQL](./concepts-pricing-tiers.md).
