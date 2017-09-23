---
title: "Загрузка данных в хранилище данных Azure с помощью Redgate | Документация Майкрософт"
description: "Сведения об использовании партнерского решения Redgate Data Platform Studio в сценариях хранения данных."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: a38b237d5bfc0450c1ca79b53a5784dbb9bf8602
ms.contentlocale: ru-ru
ms.lasthandoff: 07/11/2017

---
# <a name="load-data-with-redgate-data-platform-studio"></a>Загрузка данных с помощью Redgate Data Platform Studio
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Фабрика данных](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase;](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

В этом руководстве приведены сведения о том, как переместить данные из локальной базы данных SQL Server в хранилище данных SQL Azure с помощью партнерского решения [Redgate Data Platform Studio](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS). Так как к Data Platform Studio применены наиболее подходящие оптимизации и исправления совместимости, это самый быстрый способ начать работу с хранилищем данных SQL.

> [!NOTE]
> Компания [Redgate](http://www.red-gate.com) — это многолетний партнер корпорации Майкрософт, который предоставляет различные средства для базы данных SQL Server. Решение Data Platform Studio доступно бесплатно для коммерческого и некоммерческого использования.
> 
> 

## <a name="before-you-begin"></a>Перед началом работы
### <a name="create-or-identify-resources"></a>Создание или определение ресурсов
Для работы с этим руководством потребуется:

* **Локальная база данных SQL Server.** Данные, которые нужно импортировать в хранилище данных SQL, должны храниться в локальной базе данных SQL Server (версии 2008R2 или более поздней). Data Platform Studio не может импортировать данные напрямую из базы данных SQL Azure или из текстовых файлов.
* **Учетная запись хранения Azure.** Перед отправкой данных в хранилище данных SQL средство Data Platform Studio размещает данные в хранилище BLOB-объектов Azure. Учетная запись хранения должна быть развернута с использование модели Resource Manager (по умолчанию), а не с помощью классической модели развертывания. Если у вас нет учетной записи хранения, создайте ее. 
* **Хранилище данных SQL.** В этом руководстве мы будем перемещать данные из локальной базы данных SQL Server в хранилище данных SQL. Для этого нам потребуется интернет-хранилище данных. Если у вас еще нет хранилища данных, создайте его.

> [!NOTE]
> Чтобы оптимизировать производительность, учетную запись хранения и хранилище данных необходимо создать в одном регионе.
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Шаг 1. Вход в Data Platform Studio с помощью учетной записи Azure
Откройте браузер и перейдите на веб-сайт [Data Platform Studio](https://www.dataplatformstudio.com/). Войдите в систему с помощью учетной записи Azure, используемой для создания учетной записи хранения и хранилища данных. Если адрес электронной почты связан с рабочей или учебной учетной записью и учетной записью Майкрософт, выберите учетную запись с доступом к своим ресурсам.

> [!NOTE]
> При первом использовании Data Platform Studio появится запрос на предоставление приложению разрешений на управление ресурсами Azure.
> 
> 

## <a name="step-2-start-the-import-wizard"></a>Шаг 2. Запуск мастера импорта
В главном окне DPS щелкните ссылку Import to Azure SQL Data Warehouse (Импортировать в хранилище данных SQL Azure), чтобы запустить мастер импорта.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Шаг 3. Установка шлюза Data Platform Studio
Чтобы подключиться к локальной базе данных SQL Server, необходимо установить шлюз DPS. Шлюз — это агент клиента, который обеспечивает доступ к локальной среде, извлекает данные и отправляет их в учетную запись хранения. Данные никогда не проходят через серверы Redgate. Чтобы установить шлюз, выполните следующее:

1. Щелкните ссылку **Create Gateway** (Создать шлюз).
2. Скачайте и установите шлюз с помощью предоставленного установщика.

![][2]

> [!NOTE]
> Шлюз можно установить на любой машине с сетевым доступом к базе данных-источнику SQL Server. При доступе к базе данных SQL Server выполняется проверка подлинности Windows на основе учетных данных текущего пользователя.
> 
> 

После установки состояние шлюза изменится на Connected (Подключен). Нажмите кнопку Next (Далее).

## <a name="step-4-identify-the-source-database"></a>Шаг 4. Определение базы данных-источника
В текстовом поле *Enter Server Name* (Введите имя сервера) введите имя сервера, на котором размещены базы данных, и нажмите кнопку **Next** (Далее). В раскрывающемся меню выберите базу данных, из которой нужно импортировать данные.

![][3]

DPS проверяет таблицы для импорта в выбранной базе данных. По умолчанию DPS импортирует все таблицы в базе данных. Чтобы выбрать таблицы или отменить их выбор, щелкните ссылку All Tables (Все таблицы). Нажмите кнопку Next (Далее), чтобы перейти к следующему окну.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Шаг 5. Выбор учетной записи хранения для размещения данных
DPS запрашивает расположение для размещения данных. Выберите имеющуюся учетную запись хранения в подписке и нажмите кнопку **Next** (Далее).

> [!NOTE]
> DPS создаст в выбранной учетной записи хранения контейнер больших двоичных объектов. Для каждой операции импорта будет использоваться отдельная папка.
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Шаг 6. Выбор хранилища данных
Далее выберите интернет-базу данных в [хранилище данных SQL Azure](http://aka.ms/sqldw), в которую нужно импортировать данные. Выбрав базу данных, введите учетные данные, чтобы подключиться к ней, и нажмите кнопку **Next** (Далее).

![][5]

> [!NOTE]
> DPS объединяет исходные таблицы данных в хранилище данных. Если из-за именования таблицы нужно перезаписать имеющиеся таблицы в хранилище данных, DPS выводит предупреждение. При необходимости из хранилища данных можно удалить любые имеющиеся объекты. Для этого перед импортом установите флажок Delete all existing objects (Удалить все имеющиеся объекты).
> 
> 

## <a name="step-7-import-the-data"></a>Шаг 7. Импорт данных
В DPS появится запрос на подтверждение импорта данных. Чтобы начать импорт данных, просто нажмите кнопку Start import (Начать импорт).

![][6]

В DPS ход выполнения извлечения и передачи данных из локальной базы данных SQL Server, а также их импорта в хранилище данных SQL, визуализирован.

![][7]

После завершения импорта DPS отображает сводку по импорту данных и отчет об изменениях на основе выполненных исправлений совместимости.

![][8]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о просмотре данных в хранилище данных SQL см. в следующих источниках:

* [Запросы к хранилищу данных SQL Azure (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]
* [Визуализация данных с помощью Power BI][Visualize data with Power BI]

Дополнительные сведения о решении Redgate Data Platform Studio см. в следующих источниках:

* [Домашняя страница DPS](http://www.dataplatformstudio.com/)
* [Демонстрационное видео DPS на Channel 9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Общие сведения о других способах переноса и отправки данных в хранилище данных SQL см. в следующих источниках:

* [Перенос решения в хранилище данных SQL][Migrate your solution to SQL Data Warehouse]
* [Загрузка данных в хранилище данных Azure SQL](sql-data-warehouse-overview-load.md)

Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

