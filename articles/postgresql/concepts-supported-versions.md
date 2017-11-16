---
title: "Поддерживаемые версии в базе данных Azure для PostgreSQL | Документация Майкрософт"
description: "Описываются поддерживаемые версии в базе данных Azure для PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 646e95f5c3c7bc9bb175a5532435b28bb998b49b
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="supported-postgresql-database-versions"></a>Поддерживаемые версии базы данных PostgreSQL
Корпорация Майкрософт планирует реализовать поддержку ядра PostgreSQ версии n-2 в базе данных Azure для службы PostgreSQL (выпущенная основная версия (n) и две предыдущие основные версии (-2)).

В настоящее время база данных Azure для PostgreSQL поддерживает следующие версии:

## <a name="postgresql-version-962"></a>PostgreSQL версии 9.6.2
Сведения об улучшениях и исправлениях в PostgreSQL 9.6.2 см. в [документации по PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-2.html).

## <a name="postgresql-version-957"></a>PostgreSQL версии 9.5.7
Сведения об улучшениях и исправлениях в PostgreSQL 9.5.7 см. в [документации по PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-7.html).

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
База данных Azure для PostgreSQL автоматически управляет установкой исправлений для обновления дополнительного номера версии. Пока служба доступна в общедоступной предварительной версии, обновление основного номера версии не поддерживается. Например, обновление с версии PostgreSQL 9.5 до PostgreSQL 9.6 не поддерживается.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о поддержке различных расширений PostgreSQL см. в статье [Использование расширений PostgreSQL в базе данных Azure для PostgreSQL](concepts-extensions.md).
