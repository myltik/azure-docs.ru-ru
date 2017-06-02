---
title: "Вводные сведения о каталоге U-SQL Azure Data Lake Analytics | Документация Майкрософт"
description: "Вводные сведения о каталоге U-SQL аналитики озера данных Azure"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 55fef96f-e941-4d09-af5e-dd7c88c502b2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: ef766161f53b92003c266f26af10a37790d8fad8
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---
# <a name="use-azure-data-lake-analytics-u-sql-catalog"></a>Использование каталога U-SQL Azure Data Lake Analytics
Каталог U-SQL используется для структурирования данных и кода, чтобы их могли совместно использовать сценарии U-SQL. Каталог обеспечивает максимальную производительность, возможную с данными в озере данных Azure.

Каждая учетная запись аналитики озера данных Azure имеет ровно один связанный с ней каталог U-SQL. Невозможно удалить каталог U-SQL. В настоящее время каталоги U-SQL не могут совместно использоваться учетными записями хранилища озера данных.

Каждый каталог U-SQL содержит базу данных с названием **Master**. Базу данных Master удалить невозможно.  Каждый каталог U-SQL может содержать несколько дополнительных баз данных.

База данных U-SQL содержит следующее.

* Сборки: для совместного использования кода .NET между скриптами U-SQL.
* Функции табличных значений: для совместного использования кода U-SQL между скриптами U-SQL.
* Таблицы: для совместного использования данных между скриптами U-SQL.
* Схемы: для совместного использования табличных схем между скриптами U-SQL.

## <a name="manage-catalogs"></a>Управление каталогами
Каждая учетная запись аналитики озера данных Azure имеет связанную с ней учетную запись хранилища озера данных Azure по умолчанию. Эта учетная запись хранилища озера данных называется учетной записью хранения озера данных по умолчанию. Каталог U-SQL хранится в учетной записи хранилища озера данных по умолчанию в папке /catalog. Не удаляйте файлы из папки /catalog.

### <a name="use-azure-portal"></a>Использование портала Azure
См. раздел [Управление аналитикой озера данных с помощью портала](data-lake-analytics-manage-use-portal.md#manage-data-lake-analytics-accounts).

### <a name="use-data-lake-tools-for-visual-studio"></a>Использование средств озера данных для Visual Studio.
Можно воспользоваться средствами озера данных для Visual Studio, чтобы управлять каталогом.  Дополнительные сведения об инструментах см. в разделе [Using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (Использование Data Lake Tools для Visual Studio).

**Управление каталогом**

1. Откройте Visual Studio и подключитесь к Azure. Инструкции см. в разделе [Подключение к Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
2. Откройте **обозреватель сервера**, нажав клавиши**CTRL+ALT+S**.
3. В **обозревателе сервера** разверните узлы **Azure**, **Data Lake Analytics**, разверните учетную запись Data Lake Analytics, а затем последовательно разверните узлы **Базы данных** и **мастер**.

    - Чтобы добавить новую базу данных, щелкните правой кнопкой мыши **База данных** и нажмите кнопку **Создать базу данных**.
    - Чтобы добавить новую сборку, щелкните правой кнопкой мыши **Сборки**, а затем нажмите кнопку **Зарегистрировать сборку**.
    - Чтобы добавить новую схему, щелкните правой кнопкой мыши **Схемы**, а затем нажмите кнопку **Создать схему**.
    - Чтобы добавить новую таблицу, щелкните правой кнопкой мыши **Таблицы**, а затем нажмите кнопку **Создать таблицу**.
    - Сведения о добавлении новой функции табличных значений см. в статье [Разработка определяемых пользователем операторов U-SQL для заданий Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Обзор каталогов U-SQL Visual Studio](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)

## <a name="see-also"></a>Дополнительные материалы
* Приступая к работе

  * [Начало работы с аналитикой озера данных с помощью портала Azure](data-lake-analytics-get-started-portal.md)
  * [Начало работы с аналитикой озера данных с помощью Azure PowerShell](data-lake-analytics-get-started-powershell.md)
  * [Начало работы с аналитикой озера данных с помощью пакета SDK Azure .NET](data-lake-analytics-get-started-net-sdk.md)
  * [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
  * [Начало работы с языком U-SQL в аналитике озера данных Azure](data-lake-analytics-u-sql-get-started.md)
* U-SQL и разработка

  * [Начало работы с языком U-SQL в аналитике озера данных Azure](data-lake-analytics-u-sql-get-started.md)
  * [Использование оконных функций U-SQL для заданий в службе аналитики озера данных Azure](data-lake-analytics-use-window-functions.md)
  * [Разработка определяемых пользователем операторов U-SQL для заданий Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* управления

  * [Управление аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-manage-use-portal.md)
  * [Управление аналитикой озера данных Azure с помощью Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
  * [Устранение неполадок с заданиями аналитики озера данных Azure с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
* Полный учебник

  * [Использование интерактивных учебников по аналитике озера данных Azure](data-lake-analytics-use-interactive-tutorials.md)
  * [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md)

