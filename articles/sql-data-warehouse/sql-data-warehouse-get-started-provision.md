---
title: "Создание хранилища данных SQL на портале Azure | Документация Майкрософт"
description: "Узнайте, как создать хранилище данных SQL на портале Azure."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 552e496e-d560-419c-9996-6bbc80c521cb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2cf6e81d6a74c4a5beff91a3df90780a83bf1327


---
# <a name="create-an-azure-sql-data-warehouse"></a>Создание хранилища данных SQL Azure
> [!div class="op_single_selector"]
> * [Портал Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

При работе с этим руководством мы создадим хранилище данных SQL, содержащее пример базы данных AdventureWorksDW. Для создания хранилища будет использоваться портал Azure.

## <a name="prerequisites"></a>Предварительные требования
Для начала работы необходимы перечисленные ниже компоненты и данные.

* **Учетная запись Azure.** Чтобы создать учетную запись, перейдите на страницу [бесплатной пробной версии Azure][Бесплатная пробная версия Azure] или [кредитов Azure MSDN][Кредиты Azure MSDN].
* **Сервер SQL Azure**. См. сведения в статье, посвященной [созданию логического сервера базы данных SQL с помощью портала Azure][Create an Azure SQL Database logical server with the Azure portal].

> [!NOTE]
> Создание хранилища данных SQL может привести к дополнительным расходам.  Дополнительные сведения о ценах см. на странице с [ценами на хранилище данных SQL][SQL Data Warehouse pricing].
> 
> 

## <a name="create-a-sql-data-warehouse"></a>Создание хранилища данных SQL
1. Войдите на [портал Azure](https://portal.azure.com).
2. Последовательно выберите **+ Создать** > **Данные+хранилище** > **Хранилище данных SQL**.
   
    ![Создание](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. В колонке **Хранилище данных SQL** укажите требуемые сведения и нажмите кнопку "Создать", чтобы создать хранилище.
   
    ![Создание базы данных](./media/sql-data-warehouse-get-started-provision/create-database.png)
   
   * **Сервер**. Рекомендуется сначала выбрать сервер.  
   * **Имя базы данных**. Имя, которое будет использоваться при обращении к хранилищу данных SQL.  Это должно быть уникальное имя на сервере.
   * **Производительность**. Рекомендуем начать с 400 [DWU][DWU]. Можно переместить ползунок влево или вправо, чтобы настроить производительность хранилища данных, а также увеличить или уменьшить масштаб после создания.  Дополнительные сведения о DWU см. в статье о [масштабировании](sql-data-warehouse-manage-compute-overview.md) или на [странице цен][цены на хранилище данных SQL]. 
   * **Подписка**. Выберите [подписку], для которой будут выставляться счета за работу с хранилищем данных SQL.
   * **Группа ресурсов**. [Группы ресурсов][Группа ресурсов] — это контейнеры, которые помогают управлять коллекцией ресурсов Azure. Дополнительная информация о [группах ресурсов](../azure-resource-manager/resource-group-overview.md).
   * **Выбор источника**. Последовательно щелкните **Выбрать источник** > **Пример**. Azure автоматически подставляет в поле **Выберите пример** вариант AdventureWorksDW.

> [!NOTE]
> Параметры сортировки по умолчанию для хранилища данных SQL: SQL_Latin1_General_CP1_CI_AS. Если требуются другие параметры сортировки, можно использовать [T-SQL][T-SQL] для создания базы данных с другими параметрами сортировки.
> 
> 

1. Щелкните **Создать** , чтобы создать хранилище данных SQL.
2. Подождите несколько минут. Когда хранилище данных будет готово, вы будете перенаправлены на [портал Azure](https://portal.azure.com). Хранилище данных SQL можно найти на панели мониторинга в списке баз данных SQL или в группе ресурсов, в которой оно было создано. 
   
    ![Портал](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Дальнейшие действия
Создав хранилище данных SQL, вы можете [подключиться](sql-data-warehouse-connect-overview.md) к нему и выполнять различные запросы.

Общие сведения о загрузке см. в статье [Загрузка данных в хранилище данных SQL](sql-data-warehouse-overview-load.md).

Если вы пытаетесь перенести существующую базу данных в хранилище данных SQL, см. статью [Перенос решения в хранилище данных SQL](sql-data-warehouse-overview-migrate.md) или используйте [служебную программу миграции](sql-data-warehouse-migrate-migration-utility.md).

Можно также настроить правила брандмауэра с помощью Transact-SQL. Дополнительные сведения см. в документации [sp_set_firewall_rule][sp_set_firewall_rule] и [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

Рекомендуем также ознакомиться со статьей [Рекомендации по использованию хранилища данных SQL Azure][Best practices].

<!--Article references-->
[Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Настройка базы данных: создание группы ресурсов, сервера и правила брандмауэра]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[группы ресурсов]: ../resource-group-template-deploy-portal.md
[Рекомендации по использованию хранилища данных SQL Azure]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[подписка]: ../azure-glossary-cloud-terminology.md#subscription
[группа ресурсов]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[Цены на хранилище данных SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[бесплатной пробной версии Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[деньги на счете в Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F




<!--HONumber=Nov16_HO2-->


