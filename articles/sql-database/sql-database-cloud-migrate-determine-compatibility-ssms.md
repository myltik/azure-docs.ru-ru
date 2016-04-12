<properties
   pageTitle="Определение совместимости Базы данных SQL с помощью SSMS"
   description="База данных SQL Microsoft Azure, миграция базы данных, совместимость базы данных SQL, мастер экспорта приложений на уровне данных "
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
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Определение совместимости Базы данных SQL с помощью SSMS

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
 
В этой статье вы узнаете, как определить, подходит ли база данных SQL Server для миграции с помощью мастера экспорта приложений на уровне данных в среде SQL Server Management Studio.

## Использование среды SQL Server Management Studio

1. Убедитесь, что у вас установлена последняя версия среды SQL Server Management Studio. Новые версии среды Management Studio обновляются ежемесячно для поддержания синхронизации с обновлениями портала Azure.

 	 > [AZURE.IMPORTANT] Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Откройте среду Management Studio и подключитесь к исходной базе данных в обозревателе объектов.
3. В обозревателе объектов щелкните правой кнопкой мыши исходную базу данных, наведите указатель мыши на пункт **Задачи** и выберите команду **Экспорт приложения уровня данных...**.

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. В окне мастера экспорта щелкните **Далее** и на вкладке **Параметры** настройте сохранение BACPAC-файла на локальный диск или в большой двоичный объект Azure. BACPAC-файл будет сохранен, только если нет проблем совместимости базы данных. При наличии проблем совместимости они будут отображаться на консоли.

	![Параметры экспорта](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Перейдите на вкладку **Дополнительно** и снимите флажок **Выделить все**, чтобы пропустить экспорт данных. На этом этапе нашей целью является только проверка совместимости.

	![Параметры экспорта](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Нажмите кнопку **Далее**, а затем — кнопку **Готово**. При наличии проблем совместимости базы данных они отобразятся, после того как мастер проверит схему.

	![Параметры экспорта](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Если проблемы отсутствуют, значит база данных совместима и все готово к переносу. Если проблемы есть, их необходимо устранить. Чтобы просмотреть проблемы, нажмите ссылку **Ошибка** для соответствующей **Проверки схемы**. ![Параметры экспорта](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.	Если файл *. BACPAC успешно создан, то ваша база данных совместима с Базой данных SQL и вы готовы к миграции.

## Дальнейшее действие: устранение проблем совместимости (если есть)

[Устранение проблем совместимости базы данных](sql-database-cloud-migrate-fix-compatibility-issues.md) (если есть).

<!---HONumber=AcomDC_0316_2016-->