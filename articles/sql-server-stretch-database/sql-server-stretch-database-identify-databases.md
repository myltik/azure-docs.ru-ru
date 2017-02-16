---
title: "Определение баз данных и таблиц для базы данных Stretch в Azure | Документация Майкрософт"
description: "Узнайте, как определить базы данных и таблицы, которые подходят для растяжения баз данных."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 9253c5a2-f135-4782-95ec-8ff39454c2c0
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: bcb0a66425439522e0c9a353798ac70505b91e39
ms.openlocfilehash: 0f1a7feea79c73d7b80343fcc9898509268d111c


---
# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Определение баз данных и таблиц для растяжения с помощью Помощника по растяжению баз данных
Для определения баз данных и таблиц, которые подходят для использования базы данных Stretch, скачайте помощник по обновлению SQL Server 2016 и запустите помощник по базе данных Stretch. Помощник по базе данных Stretch также определит критические препятствия.

## <a name="download-and-install-upgrade-advisor"></a>Скачивание и установка Помощника по обновлению
Скачайте и установите Помощник по обновлению [здесь](http://go.microsoft.com/fwlink/?LinkID=613421). Это средство не включено в состав установочного носителя SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Запуск Помощника по растяжению баз данных
1. Запустите Помощник по обновлению.
2. Выберите **Сценарии**, а затем — **Run Stretch Database Advisor** (Запустить помощник по базам данных Stretch).
3. В колонке **Run Stretch Database Advisor** (Запуск Помощника по базам данных Stretch) щелкните **Select databases to analyze** (Выбрать базы данных для анализа).
4. В колонке **Выбор баз данных** введите или выберите имя сервера и данные аутентификации. Щелкните **Подключить**.
5. Появится список баз данных на выбранном сервере. Выберите базы данных, которые нужно проанализировать. Нажмите кнопку **Выбрать**.
6. В колонке **Run Stretch Database Advisor** (Запуск Помощника по базам данных Stretch) щелкните **Запустить**.  Будет запущен анализ.

## <a name="review-the-results"></a>Просмотр результатов
1. После завершения анализа в колонке **Analyzed databases** (Проанализированные базы данных) выберите одну из баз данных, чтобы отобразить колонку **Результаты анализа**.

   В колонке **Результаты анализа** будут перечислены рекомендуемые таблицы в выбранной базе данных, которые соответствуют рекомендуемым критериям по умолчанию.
2. В списке таблиц в колонке **Результаты анализа** выберите одну из рекомендуемых таблиц, чтобы открыть колонку **Table results** (Результаты для таблицы).

   При наличии критических проблем для выбранной таблицы они будут перечислены в колонке **Table results** (Результаты для таблицы). Сведения о критических проблемах, обнаруженных Помощником по базам данных Stretch, см. в статье [Ограничения для базы данных Stretch](sql-server-stretch-database-limitations.md).
3. Из списка критических проблем в колонке **Table results** (Результаты таблицы) выберите одну из проблем, чтобы просмотреть дополнительные сведения о ней и предлагаемые действия по устранению. Выполните действия по устранению проблемы, если хотите настроить выбранную таблицу для растяжения базы данных.

## <a name="next-step"></a>Дальнейшие действия
Включите растяжение базы данных.

* Чтобы включить базу данных Stretch для **базы данных**, обратитесь к разделу [Enable Stretch Database for a database](sql-server-stretch-database-enable-database.md)(Включение базы данных Stretch для базы данных).
* Чтобы включить базу данных Stretch для другой **таблицы**, если она уже включена для базы данных, обратитесь к разделу [Enable Stretch Database for a table](sql-server-stretch-database-enable-table.md)(Включение базы данных Stretch для таблицы).

## <a name="see-also"></a>Дополнительные материалы
[Ограничения для базы данных Stretch](sql-server-stretch-database-limitations.md)

[Включение растяжения базы данных для базы данных](sql-server-stretch-database-enable-database.md)

[Включение растяжения базы данных для таблицы](sql-server-stretch-database-enable-table.md)



<!--HONumber=Jan17_HO4-->


