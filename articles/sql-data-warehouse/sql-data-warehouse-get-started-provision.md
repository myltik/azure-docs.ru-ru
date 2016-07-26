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
   ms.date="06/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Создание хранилища данных SQL Azure

> [AZURE.SELECTOR]
- [Портал Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

При работе с этим руководством вы будете использовать портал Azure, чтобы создать хранилище данных SQL, содержащее пример базы данных AdventureWorksDW.


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


1. Войдите на [портал Azure](https://portal.azure.com).

2. Последовательно выберите **+ Создать** > **Данные+хранилище** > **Хранилище данных SQL**.

    ![Создание](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. В колонке **Хранилище данных SQL** укажите требуемые сведения и нажмите кнопку "Создать", чтобы создать хранилище.

    ![Создание базы данных](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **Сервер**. Рекомендуется сначала выбрать сервер. Можно выбрать существующий сервер или [создать сервер](./sql-data-warehouse-get-started-new-server.md).

	- **Имя базы данных**. Имя, которое будет использоваться для обращения к хранилищу данных SQL. Это должно быть уникальное имя на сервере.
	
    - **Производительность**. Мы рекомендуем начать с 400 DWU. Можно переместить ползунок влево или вправо, чтобы настроить производительность хранилища данных, а также увеличить или уменьшить масштаб после создания. Дополнительные сведения о DWU см. в статье о [масштабировании](./sql-data-warehouse-manage-compute-overview.md) или на [странице цен](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

    - **Подписка**. Выберите подписку, на которую хранилище данных SQL будет выставлять счета.

    - **Группа ресурсов**. Группы ресурсов — это контейнеры, которые помогают управлять коллекцией ресурсов Azure. Дополнительная информация о [группах ресурсов](../resource-group-overview.md).

    - **Выбрать источник**. Последовательно выберите **Выбрать источник** > **Пример**. Так как сейчас есть только один пример базы данных, когда вы выбираете элемент «Образец», Azure автоматически устанавливает значение AdventureWorksDW для параметра **Выберите пример**.

4. Нажмите кнопку **Создать**, чтобы создать хранилище данных SQL.

5. Подождите несколько минут, пока создается хранилище данных SQL. Когда база данных будет готова, вы вернетесь на [портал Azure](https://portal.azure.com). Хранилище данных SQL можно найти на панели мониторинга в списке баз данных SQL или в группе ресурсов, в которой оно было создано.

    ![Предварительная версия портала](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [Создание сервера базы данных SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Дальнейшие действия

Теперь, когда создано хранилище данных SQL, вы можете [подключиться](./sql-data-warehouse-connect-overview.md) и приступить к выполнению запросов.

Общие сведения о загрузке см. в статье [Загрузка данных в хранилище данных SQL](./sql-data-warehouse-overview-load.md).

Если вы пытаетесь перенести существующую базу данных в хранилище данных SQL, см. статью [Общие сведения о миграции](./sql-data-warehouse-overview-migrate.md) или используйте [служебную программу миграции](./sql-data-warehouse-migrate-migration-utility.md).

<!---HONumber=AcomDC_0720_2016-->