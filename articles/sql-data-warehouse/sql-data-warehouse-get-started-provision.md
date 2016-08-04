<properties
   pageTitle="Создание хранилища данных SQL на портале Azure | Microsoft Azure"
   description="Узнайте, как создать хранилище данных SQL на портале Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/23/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# Создание хранилища данных SQL Azure

> [AZURE.SELECTOR]
- [Портал Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

При работе с этим руководством вы будете использовать портал Azure, чтобы создать хранилище данных SQL, содержащее пример базы данных AdventureWorksDW.


## Предварительные требования

Для начала работы вам потребуются перечисленные ниже компоненты и данные.

- **Учетная запись Azure**. Чтобы создать учетную запись, перейдите на страницу [бесплатной пробной версии Azure][] или [кредитов Azure MSDN][].
- **Сервер SQL Azure**. Cм. сведения в статье о [создании логического сервера базы данных SQL с помощью портала Azure][].

> [AZURE.NOTE] Создание хранилища данных SQL может привести к дополнительным расходам. Дополнительные сведения о ценах см. на странице [Цены на хранилище данных SQL][].

## Создание хранилища данных SQL

1. Войдите на [портал Azure](https://portal.azure.com).

2. Последовательно выберите **+ Создать** > **Данные+хранилище** > **Хранилище данных SQL**.

    ![Создание](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. В колонке **Хранилище данных SQL** укажите требуемые сведения и нажмите кнопку "Создать", чтобы создать хранилище.

    ![Создание базы данных](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **Сервер**. Рекомендуется сначала выбрать сервер. Можно выбрать имеющийся сервер или [создать сервер](./sql-data-warehouse-get-started-new-server.md).

	- **Имя базы данных**. Имя, которое будет использоваться для обращения к хранилищу данных SQL. Это должно быть уникальное имя на сервере.
	
    - **Производительность**. Рекомендуем начать с 400 [DWU][DWU]. Можно переместить ползунок влево или вправо, чтобы настроить производительность хранилища данных, а также увеличить или уменьшить масштаб после создания. Дополнительные сведения о DWU см. в статье о [масштабировании](./sql-data-warehouse-manage-compute-overview.md) или на [странице цен][SQL Data Warehouse pricing].

    - **Подписка**. Выберите [подписку], на которую хранилище данных SQL будет выставлять счета.

    - **Группа ресурсов**. [Группы ресурсов][Resource group] — это контейнеры, которые помогают управлять коллекцией ресурсов Azure. Дополнительная информация о [группах ресурсов](../resource-group-overview.md).

    - **Выбрать источник**. Последовательно выберите **Выбрать источник** > **Пример**. Так как сейчас есть только один пример базы данных, когда вы выбираете элемент «Образец», Azure автоматически устанавливает значение AdventureWorksDW для параметра **Выберите пример**.

4. Щелкните **Создать**, чтобы создать хранилище данных SQL.

5. Подождите несколько минут, пока создается хранилище данных SQL. Когда база данных будет готова, вы вернетесь на [портал Azure](https://portal.azure.com). Хранилище данных SQL можно найти на панели мониторинга в списке баз данных SQL или в группе ресурсов, в которой оно было создано.

    ![Предварительная версия портала](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [Создание сервера базы данных SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Дальнейшие действия

Теперь, когда создано хранилище данных SQL, вы можете [подключиться](./sql-data-warehouse-connect-overview.md) и приступить к выполнению запросов.

Общие сведения о загрузке см. в статье [Загрузка данных в хранилище данных SQL](./sql-data-warehouse-overview-load.md).

Если вы пытаетесь перенести существующую базу данных в хранилище данных SQL, см. статью [Общие сведения о миграции](./sql-data-warehouse-overview-migrate.md) или используйте [служебную программу миграции](./sql-data-warehouse-migrate-migration-utility.md).

Можно также настроить правила брандмауэра с помощью Transact-SQL. Дополнительные сведения см. в разделе [sp\_set\_firewall\_rule][] и [sp\_set\_database\_firewall\_rule][].

Рекомендуем ознакомиться со статьей [Рекомендации по использованию хранилища данных SQL Azure][].

<!--Article references-->
[создании логического сервера базы данных SQL с помощью портала Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[Рекомендации по использованию хранилища данных SQL Azure]: ./sql-data-warehouse-best-practices.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[подписку]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group

<!--MSDN references-->
[sp\_set\_firewall\_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp\_set\_database\_firewall\_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Цены на хранилище данных SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[бесплатной пробной версии Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[кредитов Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0727_2016-->