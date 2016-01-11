<properties
   pageTitle="Миграция базы данных SQL Server в Базу данных SQL с помощью мастера развертывания базы данных в Базу данных Microsoft Azure"
   description="База данных SQL Microsoft Azure, миграция базы данных, мастер баз данных Microsoft Azure"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Миграция базы данных SQL Server в Базу данных SQL с помощью мастера развертывания базы данных в Базу данных Microsoft Azure

Мастер развертывания базы данных в базу данных Microsoft Azure в среде SQL Server Management Studio переносит [совместимую базу данных SQL Server](../sql-database-cloud-migrate.md) непосредственно на сервер Базы данных SQL Azure.

## Использование мастера развертывания базы данных в Базу данных Microsoft Azure

> [AZURE.NOTE]В описанных ниже действиях предполагается, что у вас есть [подготовленный сервер Базы данных SQL](../sql-database-get-started.md).

1. Убедитесь, что у вас установлена последняя версия среды SQL Server Management Studio. Новые версии среды Management Studio обновляются ежемесячно для поддержания синхронизации с обновлениями портала Azure.

    > [AZURE.IMPORTANT]Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Откройте среду Management Studio и подключитесь к базе данных SQL Server, которую нужно перенести, в обозревателе объектов.
3. В обозревателе объектов щелкните базу данных правой кнопкой мыши, наведите указатель мыши на пункт **Задачи** и выберите команду **Развернуть базу данных в Базу данных SQL Microsoft Azure…**.

	![Развертывание в Azure из меню "Задачи"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	В мастере развертывания щелкните **Далее** и **Подключиться**, чтобы настроить подключение к серверу Базы данных SQL.

	![Развертывание в Azure из меню "Задачи"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. В диалоговом окне "Подключение к серверу" введите сведения о подключении, чтобы подключиться к серверу Базы данных SQL.

	![Развертывание в Azure из меню "Задачи"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.	Укажите **Имя новой базы данных** для нового имени базы данных, установите параметры **Выпуск Базы данных SQL Microsoft Azure** ([уровень службы](sql-database-service-tiers.md)), **Максимальный размер базы данных**, **Цель обслуживания** (уровень производительности) и **Имя временного файла** для [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файла, создаваемого мастером в процессе переноса.

	![Параметры экспорта](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	Завершите выполнение мастера, чтобы перенести базу данных. В зависимости от размера и сложности базы данных процесс развертывания может занять от нескольких минут до нескольких часов. Если мастер выявляет проблемы совместимости, ошибки отображаются на экране и миграция останавливается. Руководство по устранению проблем совместимости базы данных см. в разделе [Устранение проблем совместимости базы данных](sql-database-cloud-migrate-fix-compatibility-issues).

7.	В обозревателе объектов подключитесь к перенесенной базе данных на сервере базы данных SQL Azure.
8.	На портале Azure просмотрите базу данных и ее свойства.

## Дальнейшее действие: устранение проблем совместимости (если есть)

[Устранение проблем совместимости базы данных](sql-database-cloud-migrate-fix-compatibility-issues.md) (если есть).

<!---HONumber=AcomDC_1223_2015-->