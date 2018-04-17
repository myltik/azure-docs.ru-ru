---
title: Загрузка данных из SQL Server в хранилище данных SQL Azure (SSIS) | Документация Майкрософт
description: Здесь показано, как создать пакет служб SQL Server Integration Services (SSIS) для перемещения данных из разнообразных источников данных в хранилище данных SQL.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: ''
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2018
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Загрузка данных из SQL Server в хранилище данных SQL Azure (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase;](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Для загрузки данных из SQL Server в хранилище данных SQL Azure можно создать пакет служб SQL Server Integration Services (SSIS). При желании вы можете изменить структуру данных, преобразовать и очистить их при прохождении через поток данных служб SSIS.

Изучив данный учебник, вы научитесь:

* создание нового проекта служб Integration Services в Visual Studio;
* подключение к источникам данных, в том числе SQL Server (в качестве источника) и хранилищу данных SQL (в качестве назначения);
* создание пакета служб SSIS, который загружает данные из источника в место назначения;
* запуск пакета служб SSIS для загрузки данных.

В этом руководстве в качестве источника данных используется SQL Server. SQL Server может выполняться на локальном компьютере или на виртуальной машине Azure.

## <a name="basic-concepts"></a>Основные понятия
Пакет — это единица работы в службах SSIS. Связанные пакеты группируются в проекты. Вы можете создавать проекты и разрабатывать пакеты с помощью SQL Server Data Tools для Visual Studio. Разработка выполняется в графическом интерфейсе, где вы перетаскиваете компоненты из области элементов в область конструктора, затем соединяете их и задаете их свойства. Когда пакет будет готов, вы можете развернуть его на SQL Server, который предоставляет широкие возможности для управления, мониторинга и защиты.

## <a name="options-for-loading-data-with-ssis"></a>Параметры для загрузки данных с помощью служб SSIS
SQL Server Integration Services (SSIS) — это гибкий набор средств, предоставляющий различные возможности для подключения к хранилищу данных SQL и загрузки данных в это хранилище.

1. Подключение к хранилищу данных SQL с использованием назначения ADO NET. В этом руководстве используется именно назначение ADO NET, так как у него меньше всего параметров конфигурации.
2. Подключение к хранилищу данных SQL с использованием назначения OLE DB. Этот вариант позволит немного повысить производительность по сравнению с назначением ADO NET.
3. Временное размещение данных в хранилище BLOB-объектов Azure с использованием задачи загрузки в большой двоичный объект Azure. После этого следует запустить задание SSIS Execute SQL, которое запускает сценарий Polybase для загрузки данных в хранилище данных SQL. Этот вариант обеспечивает наивысшую производительность из трех перечисленных вариантов. Чтобы использовать задачу отправки большого двоичного объекта Azure, необходимо скачать [пакет дополнительных компонентов для служб Microsoft SQL Server 2016 Integration Services для Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]. Дополнительные сведения о Polybase см. в [руководстве по PolyBase][PolyBase Guide].

## <a name="before-you-start"></a>Перед началом работы
Для выполнения этих действий необходимо иметь следующее:

1. **SQL Server Integration Services (SSIS).** Службы SSIS — это компонент SQL Server. Для его использования требуется ознакомительная или лицензированная версия SQL Server. Ознакомительную версию SQL Server 2016 можно получить [на этой странице][SQL Server Evaluations].
2. **Visual Studio**. Бесплатный выпуск Visual Studio Community можно получить [на этой странице][Visual Studio Community].
3. **SQL Server Data Tools для Visual Studio (SSDT)**. SQL Server Data Tools для Visual Studio можно получить на [этой странице][Download SQL Server Data Tools (SSDT)].
4. **Образец данных**. В этом руководстве источником данных для загрузки в хранилище данных SQL является образец базы данных AdventureWorks на SQL Server. Образец базы данных AdventureWorks 2014 можно получить на [этой странице][AdventureWorks 2014 Sample Databases].
5. **База данных хранилища данных SQL и разрешения на ее использование**. В этом руководстве мы будем подключаться к экземпляру хранилища данных SQL и загружать в него данные. Вам потребуются разрешения на создание таблицы и загрузку данных.
6. **Правило брандмауэра.** Вам следует создать правило брандмауэра в хранилище данных SQL, разрешающее обращения с IP-адреса локального компьютера. Без этого вы не сможете загрузить данные в хранилище данных SQL.

## <a name="step-1-create-a-new-integration-services-project"></a>Шаг 1. Создание нового проекта служб Integration Services
1. Запустите Visual Studio.
2. В меню **Файл** выберите **Создать| Проект**.
3. Перейдите к типу проекта **Установленные | Шаблоны | Бизнес-аналитика | Службы Integration Services** .
4. Выберите **Проект служб Integration Services**. Введите **имя** и **расположение**, а затем нажмите кнопку **ОК**.

Visual Studio откроется и создаст новый проект служб Integration Services (SSIS). Затем Visual Studio откроет конструктор для одного нового пакета служб SSIS (Package.dtsx) в этом проекте. Вы увидите на экране следующие области.

* В левой части — область **Инструменты** , где представлены компоненты служб SSIS.
* В центре — область конструктора с несколькими вкладками. Из них вам потребуются по меньшей мере вкладки **Поток управления** и **Поток данных**.
* С правой стороны расположены области **Обозреватель решений** и **Свойства**.
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Шаг 2: Создание простого потока данных
1. Перетащите задачу потока данных с панели инструментов в середину области конструктора (на вкладку **Поток управления** ).
   
    ![][02]
2. Дважды щелкните задачу потока данных, чтобы перейти на вкладку "Поток данных".
3. Из списка других источников на панели инструментов перетащите в область конструктора источник ADO.NET. Выбрав адаптер источника данных, измените его имя на **Источник SQL Server** в области **Свойства**.
4. Из списка других источников на панели инструментов перетащите в область конструктора назначение ADO.NET и поместите его под источником ADO.NET. Выбрав адаптер загрузки данных, измените его имя на **Назначение SQL DW** в области **Свойства**.
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Шаг 3: Настройка адаптера источника
1. Дважды щелкните адаптер источника, чтобы открыть **редактор источника ADO.NET**.
   
    ![][03]
2. На вкладке **Диспетчер подключений** в **редакторе источника ADO.NET** нажмите кнопку **Создать** рядом со списком **Диспетчер соединений ADO.NET**. Откроется диалоговое окно **Настройка диспетчера соединений ADO.NET**, где нужно создать параметры подключения к базе данных SQL Server, из которой мы будем загружать данные в рамках этого руководства.
   
    ![][04]
3. В диалоговом окне **Настройка диспетчера соединений ADO.NET** нажмите кнопку **Создать**, чтобы открыть диалоговое окно **Диспетчер подключений** и создать новое подключение к данным.
   
    ![][05]
4. В диалоговом окне **Диспетчер подключений** выполните следующие действия.
   
   1. Для параметра **Поставщик**выберите поставщик данных SqlClient.
   2. В поле **Имя сервера**введите имя сервера SQL Server.
   3. В разделе **Вход на сервер** выберите или введите сведения для проверки подлинности.
   4. В разделе **Подключение к базе данных** выберите образец базы данных AdventureWorks.
   5. Нажмите кнопку **Проверить подключение**.
      
       ![][06]
   6. В диалоговом окне с результатами проверки подключения нажмите кнопку **ОК**, чтобы вернуться в диалоговое окно **Диспетчер подключений**.
   7. В диалоговом окне **Диспетчер подключений** нажмите кнопку **ОК**, чтобы вернуться в диалоговое окно **Настройка диспетчера соединений ADO.NET**.
5. В диалоговом окне **Настройка диспетчера соединений ADO.NET** нажмите кнопку **ОК**, чтобы вернуться в **редактор источника ADO.NET**.
6. В **редакторе источника ADO.NET** выберите в списке **Имя таблицы или представления** таблицу **Sales.SalesOrderDetail**.
   
    ![][07]
7. Щелкните **Предварительный просмотр**, чтобы открыть первые 200 строк данных из исходной таблицы в диалоговом окне **Предварительный просмотр результатов запроса**.
   
    ![][08]
8. В диалоговом окне **Предварительный просмотр результатов запроса** нажмите кнопку **Закрыть**, чтобы вернуться в редактор источника **ADO.NET**.
9. В **редакторе источника ADO.NET** нажмите кнопку **ОК**, чтобы завершить настройку источника данных.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Шаг 4. Подключение адаптера источника к адаптеру назначения
1. Выберите адаптер источника в области конструктора.
2. Выделите синюю стрелку, которая выходит из адаптера источника, и перетащите ее к адаптеру назначения, чтобы она зафиксировалась на нем.
   
    ![][10]
   
    В типичном пакете служб SSIS вы будете использовать и другие компоненты из панели инструментов SSIS. Их вы разместите между источником и назначением, чтобы выполнять изменение структуры, преобразование и очистку данных при прохождении через поток данных служб SSIS. Но в этом примере для простоты мы подключаем источник напрямую к месту назначения.

## <a name="step-5-configure-the-destination-adapter"></a>Шаг 5. Настройка адаптера назначения
1. Дважды щелкните адаптер назначения, чтобы открыть **редактор назначения ADO.NET**.
   
    ![][11]
2. На вкладке **Диспетчер подключений** в **редакторе назначения ADO.NET** нажмите кнопку **Создать** рядом со списком **Диспетчер подключений**. Откроется диалоговое окно **Настройка диспетчера соединений ADO.NET**, где нужно создать параметры подключения к базе данных хранилища данных SQL Azure, в которую мы будем загружать данные в рамках этого руководства.
3. В диалоговом окне **Настройка диспетчера соединений ADO.NET** нажмите кнопку **Создать**, чтобы открыть диалоговое окно **Диспетчер подключений** и создать новое подключение к данным.
4. В диалоговом окне **Диспетчер подключений** выполните следующие действия.
   1. Для параметра **Поставщик**выберите поставщик данных SqlClient.
   2. В поле **Имя сервера**введите имя хранилища данных SQL.
   3. В разделе **Вход на сервер** выберите **Использовать проверку подлинности SQL Server** и введите сведения для проверки подлинности.
   4. В разделе **Подключение к базе данных** выберите существующую базу данных хранилища данных SQL.
   5. Нажмите кнопку **Проверить подключение**.
   6. В диалоговом окне с результатами проверки подключения нажмите кнопку **ОК**, чтобы вернуться в диалоговое окно **Диспетчер подключений**.
   7. В диалоговом окне **Диспетчер подключений** нажмите кнопку **ОК**, чтобы вернуться в диалоговое окно **Настройка диспетчера соединений ADO.NET**.
5. В диалоговом окне **Настройка диспетчера соединений ADO.NET** нажмите кнопку **ОК**, чтобы вернуться в **редактор назначения ADO.NET**.
6. В **редакторе назначения ADO.NET** нажмите кнопку **Создать** рядом со списком **Использовать таблицу или представление**, чтобы открыть диалоговое окно **Создание таблицы**. Здесь следует создать новую целевую таблицу, столбцы которой должны совпадать со столбцами исходной таблицы.
   
    ![][12a]
7. В диалоговом окне **Создание таблицы** выполните указанные ниже действия.
   
   1. Измените имя целевой таблицы на **SalesOrderDetail**.
   2. Удалите столбец **rowguid** . Тип данных **uniqueidentifier** не поддерживается в хранилище данных SQL.
   3. Для столбца **LineTotal** измените тип данных на **money**. Тип данных **decimal** не поддерживается в хранилище данных SQL. Дополнительные сведения о поддерживаемых типах данных см. в статье [СОЗДАНИЕ ТАБЛИЦЫ (хранилище данных Azure SQL)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)].
      
       ![][12b]
   4. Нажмите кнопку **ОК**, чтобы завершить создание таблицы и вернуться в **редактор назначения ADO.NET**.
8. В **редакторе назначения ADO.NET** выберите вкладку **Сопоставления**. Здесь вы увидите, как столбцы источника сопоставлены со столбцами назначения.
   
    ![][13]
9. Щелкните **ОК** , чтобы завершить настройку источника данных.

## <a name="step-6-run-the-package-to-load-the-data"></a>Шаг 6. Запуск пакета для загрузки данных
Запустите пакет, нажав кнопку **Запустить** на панели инструментов или выбрав один из вариантов **запуска** в меню **Отладка**.

Когда пакет начнет выполняться, вы увидите желтые вращающиеся колесики, которые обозначают выполнение действия, а также количество уже обработанных строк.

![][14]

Когда завершится выполнение пакета, вы увидите зеленые галочки, подтверждающие успешное выполнение, а также общее количество рядов данных, загруженных из источника в место назначения.

![][15]

Поздравляем! Вы успешно загрузили данные в хранилище данных SQL Azure с помощью служб SQL Server Integration Services.

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше о потоке данных служб SSIS. Начните со статьи [Поток данных][Data Flow].
* Ознакомьтесь со способами отладки и устранения неполадок в работе пакетов, доступными в среде разработки. Начните со статьи [Инструменты устранения неполадок при разработке пакета][Troubleshooting Tools for Package Development].
* Узнайте, как развернуть проекты и пакеты служб SSIS на сервер служб Integration Services или в другое хранилище. Начните со статьи [Развертывание проектов и пакетов служб Integration Services (SSIS)][Deployment of Projects and Packages].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
