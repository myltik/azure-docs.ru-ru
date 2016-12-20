---
title: "Поддержка клиентов прежних версий хранилища данных SQL для аудита данных | Документация Майкрософт"
description: "Сведения о поддержке клиентов прежних версий хранилища данных SQL для аудита данных"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: dfe29ff3-dfeb-4309-83c0-c1a300f4f44e
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: rortloff;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3750e2875935d2753a819ef8ce540009417d19b5


---
# <a name="sql-data-warehouse---downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Хранилище данных SQL. Поддержка клиентов прежних версий для аудита и динамического маскирования данных
[аудита](sql-data-warehouse-auditing-overview.md) работают в клиентах SQL, которые поддерживают перенаправление TDS.

Любой клиент, который реализует TDS 7.4, также должен поддерживать перенаправление. К исключениям относятся JDBC 4.0, где функция перенаправления поддерживается не полностью, и Tedious для Node.JS, где перенаправление не реализовано.

Для клиентов прежних версий, т. е. которые поддерживают TDS 7.3 и ниже, в строке подключения необходимо изменить полное доменное имя сервера:

Полное доменное имя сервера-источника в строке подключения: <*имя сервера*>.database.windows.net.

Измененное полное доменное имя сервера в строке подключения: <*имя сервера*>.database.**secure**.windows.net.

Частичный список клиентов прежних версий включает:

* .NET 4.0 и ниже
* ODBC 10.0 и ниже
* JDBC (хотя JDBC поддерживает TDS 7.4, но функция перенаправления TDS поддерживается не полностью)
* Tedious (для Node.JS)

**Примечание.** Описанное выше изменение полного доменного имени сервера можно использовать также для применения политики аудита уровня SQL Server без необходимости настройки в каждой базе данных (временное устранение рисков).     




<!--HONumber=Nov16_HO3-->


