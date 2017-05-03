---
title: "Перенос базы данных SQL Server в базу данных SQL Azure | Документация Майкрософт"
description: "Сведения о переносе базы данных SQL Server в базу данных SQL Azure."
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: c6d965351f6f131ee342cea672fc4fa8771f8ede
ms.lasthandoff: 04/21/2017


---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Перенос базы данных SQL Server в базу данных SQL Azure

В этом руководстве приведены сведения о переносе имеющейся базы данных SQL Server с помощью Microsoft Data Migration Assistant, а также описаны действия по подготовке, фактическому переносу данных и подключению к перенесенной базе данных. 

> [!IMPORTANT]
> Чтобы устранить проблемы совместимости, используйте [средства Visual Studio для работы с данными](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). 
>

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

В рамках этого руководства вам потребуются:

- Последняя версия [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). При установке SSMS также устанавливается последняя версия SqlPackage, служебной программы командной строки, используемой для автоматизации ряда задач по разработке базы данных. 
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- База данных для переноса. В этом руководстве используется [база данных SQL Server 2008 R2 AdventureWorks OLTP](https://msftdbprodsamples.codeplex.com/releases/view/59211) в экземпляре SQL Server 2008 R2 или более поздней версии, но можно использовать любую базу данных по своему усмотрению. 

## <a name="prepare-for-migration"></a>Подготовка к переносу

Теперь вы готовы приступить к подготовке к переносу. Чтобы оценить готовность своей базы данных к переносу в базу данных SQL Azure с помощью **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)**, выполните приведенные ниже действия.

1. Откройте **Data Migration Assistant**. DMA можно запустить на любом компьютере с подключением к экземпляру SQL Server, содержащему базу данных, которую планируется перенести. Это средство не нужно устанавливать на компьютере с экземпляром SQL Server.

     ![Открытие Data Migration Assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. Чтобы создать проект **Оценка**, в меню слева щелкните **+ Создать**. Заполните форму, указав **имя проекта** (все остальные значения следует оставить по умолчанию) и нажмите кнопку **Создать**. После этого откроется страница **Параметры**.

     ![Создание проекта в Data Migration Assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3. На странице **Параметры** нажмите кнопку **Далее**. После этого откроется страница**Select sources** (Выбор источников).

     ![Создание параметров переноса данных](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-options.png) 

4. На странице **Select sources** (Выбор источников) введите имя экземпляра SQL Server, содержащего сервер для переноса. При необходимости измените другие значения на этой странице. Щелкните **Подключить**.

     ![Выбор новых источников при подготовке к переносу данных](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources.png)

5. В области **Add sources** (Добавить источники) страницы **Select sources** (Выбор источников) установите флажки напротив баз данных, совместимость которых требуется проверить. Щелкните **Добавить**.

     ![Выбор новых источников при подготовке к переносу данных](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources-add.png)

6. Нажмите кнопку **Start Assessment** (Запустить оценку).

     ![Запуск оценки при подготовке к переносу данных](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-start-assessment.png)

7. По завершении оценки проверьте, достаточен ли уровень совместимости базы данных для переноса. Найдите флажок в зеленом круге.

     ![Результаты оценки совместимости при подготовке к переносу данных](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-compatible.png)

8. Просмотрите результаты, начиная со **сравнения функций SQL Server**. В частности, просмотрите сведения о неподдерживаемых и частично поддерживаемых функциях, а также сведения о рекомендуемых действиях. 

     ![Результаты сравнения при подготовке к переносу данных](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. Щелкните **Compatibility issues** (Проблемы совместимости). Просмотрите сведения об ошибках переноса, изменениях в поведении и устаревших функциях для каждого уровня совместимости. Для базы данных AdventureWorks2008R2 просмотрите изменения в полнотекстовом поиске, начиная с SQL Server 2008, и изменения в SERVERPROPERTY('LCID'), начиная с SQL Server 2000. Дополнительные сведения об этих изменениях предоставлены по ссылкам. Многие параметры и настройки полнотекстового поиска изменились. 

   > [!IMPORTANT] 
   > После переноса базы данных в базу данных SQL Azure вы можете работать с ней при текущем уровне совместимости (уровень 100 для базы данных AdventureWorks2008R2) или при более высоком. Дополнительные сведения о последствиях и параметрах работы базы данных с определенным уровнем совместимости см. в разделе [ALTER DATABASE (Transact-SQL) Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Уровень совместимости ALTER DATABASE (Transact-SQL)). Кроме того, в статье [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) вы можете получить сведения о дополнительных настройках уровня базы данных, связанных с уровнем совместимости.
   >

10. При необходимости нажмите кнопку **Экспортировать отчет**, чтобы сохранить отчет в формате JSON.
11. Закройте Data Migration Assistant.

## <a name="export-to-bacpac-file"></a>Экспорт в BACPAC-файл 

BACPAC-файл — это ZIP-файл с расширением BACPAC, содержащий метаданные и данные из базы данных SQL Server. BACPAC-файл можно сохранить в хранилище BLOB-объектов Azure или в локальном хранилище для архивирования или переноса (например, из базы данных SQL Server в базу данных SQL Azure). Чтобы экспорт был транзакционно согласованным, в это время необходимо остановить все операции записи.

Чтобы экспортировать базу данных AdventureWorks2008R2 в локальное хранилище с помощью служебной программы командной строки SqlPackage, выполните приведенные ниже действия.

1. Откройте окно командной строки Windows и перейдите в папку, где хранится **130** версия SqlPackage (например, **C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin**).

2. Чтобы экспортировать базу данных **AdventureWorks2008R2** из **localhost** в **AdventureWorks2008R2.bacpac**, в окне командной строки выполните приведенную ниже команду SqlPackage. Измените соответствующие значения в зависимости от среды.

    ```SQLPackage
    sqlpackage.exe /Action:Export /ssn:localhost /sdn:AdventureWorks2008R2 /tf:AdventureWorks2008R2.bacpac
    ```

    ![Экспорт SqlPackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-export.png)

После выполнения созданный BCPAC-файл сохраняется в каталоге, в котором находится исполняемый файл SqlPackage. В этом примере это C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin. 

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.

Войдите на [портал Azure](https://portal.azure.com/). Если войти в систему с компьютера, на котором вы используете служебную программу командной строки SqlPackage, это упростит создание правила брандмауэра на шаге 5.

## <a name="create-a-sql-database-logical-server"></a>Создание логического сервера базы данных SQL

[Логический сервер базы данных SQL Azure](sql-database-features.md) выступает в качестве центральной точки администрирования нескольких баз данных. Чтобы создать логический сервер базы данных SQL для хранения перенесенной базы данных Adventure Works OLTP SQL Server, выполните приведенные ниже действия. 

1. Щелкните **Создать** в верхнем левом углу портала Azure.

2. На странице **Создать** в окне поиска введите **server** и в отфильтрованном списке выберите **SQL Server (логический сервер)**.

    ![выбор логического сервера](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. На странице **Все** щелкните **SQL server (логический сервер)** и на странице **SQL Server (логический сервер)** нажмите кнопку **Создать**.

    ![Создание логического сервера](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. Заполните форму "SQL Server (логический сервер)", указав следующую информацию, как показано на предыдущем изображении.     

   - Имя сервера. Укажите глобально уникальное имя сервера.
   - Имя для входа администратора сервера.
   - Пароль. Укажите необходимый пароль.
   - Группа ресурсов. Установите флажок **Создать** и укажите **myResourceGroup**.
   - Расположение. Выберите расположение центра обработки данных.

    ![Заполненная форма создания логического сервера](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. Чтобы подготовить логический сервер, нажмите кнопку **Создать**. Подготовка занимает несколько минут. 

## <a name="create-a-server-level-firewall-rule"></a>создадим правило брандмауэра на уровне сервера;

Служба базы данных SQL создает [брандмауэр уровня сервера](sql-database-firewall-configure.md), который не позволяет внешним приложениям и средствам подключаться к серверу или любой базе данных на сервере, если не создано правило брандмауэра, открывающее брандмауэр для определенных IP-адресов. Чтобы создать правило брандмауэра уровня сервера базы данных SQL для IP-адреса компьютера, на котором вы запускаете служебную программу командной строки SqlPackage, выполните приведенные ниже действия. Это позволит SqlPackage подключиться к логическому серверу базы данных SQL через брандмауэр базы данных SQL Azure. 

1. В меню слева щелкните **Все ресурсы** и на открывшейся странице **Все ресурсы** выберите новый сервер. После этого откроется страница обзора сервера, где будет указано полное имя сервера (например, **mynewserver20170403.database.windows.net**) и предоставлены параметры для дальнейшей настройки.

     ![Общие сведения о логическом сервере](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. На странице обзора на вкладке **Параметры** щелкните **Брандмауэр** в меню слева. Откроется страница **параметров брандмауэра** для сервера базы данных SQL. 

3. Щелкните **Добавить IP-адрес клиента** на панели инструментов, чтобы добавить IP-адрес используемого компьютера, а затем щелкните **Сохранить**. Для текущего IP-адреса будет создано правило брандмауэра уровня сервера.

     ![Настройка правила брандмауэра для сервера](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. Нажмите кнопку **ОК**.

Теперь можно подключиться ко всем базам данных на сервере с помощью SQL Server Management Studio или другого средства по своему усмотрению с этого IP-адреса, используя учетную запись администратора сервера, созданную ранее.

> [!NOTE]
> База данных SQL обменивается данными через порт 1433. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 1433 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу базы данных SQL Azure. Для этого ваш ИТ-отдел должен открыть порт 1433.
>

## <a name="import-bacpac-file-to-azure-sql-database"></a>Импорт BACPAC-файла в базу данных SQL Azure 

Последние версии служебной программы командной строки SqlPackage поддерживают создание базы данных SQL Azure в соответствии с указанным [уровнем служб и производительности](sql-database-service-tiers.md). Чтобы повысить производительность импорта, выберите высокий уровень производительности и служб, а затем после импорта выполните масштабирование, если уровень производительности и служб выше требуемого.

Чтобы импортировать базу данных AdventureWorks2008R2 в базу данных SQL Azure с помощью служебной программы командной строки SqlPackage, выполните приведенные ниже действия.

- Выполните приведенную ниже команду SqlPackage в окне командной строки, чтобы импортировать базу данных **AdventureWorks2008R2** из локального хранилища на логический сервер базы данных SQL Azure, созданный ранее с именем базы данных **myMigratedDatabase**, уровнем служб **Премиум** и целью обслуживания **P6**. Измените любые из этих трех значений в зависимости от среды.

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![Импорт SqlPackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Логический сервер базы данных SQL Azure прослушивает порт 1433. Чтобы подключиться к логическому серверу базы данных SQL Azure из среды, ограничиваемой корпоративным брандмауэром, этот порт должен быть открыт в брандмауэре.
>

## <a name="connect-using-sql-server-management-studio-ssms"></a>Подключение с помощью SQL Server Management Studio

SQL Server Management Studio позволяет подключиться к серверу базы данных SQL Azure и перемещенной базе данных. При запуске SQL Server Management Studio на другом компьютере (не на том, где была запущена SqlPackage) создайте правило брандмауэра для этого компьютера, следуя инструкциям в предыдущей процедуре.

1. Откройте среду SQL Server Management Studio.

2. В диалоговом окне **Подключение к серверу** введите следующие значения.
   - **Тип сервера** — укажите ядро СУБД.
   - **Имя сервера.** Введите полное имя сервера, например **mynewserver20170403.database.windows.net**.
   - **Проверка подлинности** — укажите проверку подлинности SQL Server.
   - **Имя входа** — введите учетную запись администратора сервера.
   - **Пароль** — введите пароль учетной записи администратора сервера.
 
    ![Подключение с помощью SSMS](./media/sql-database-migrate-your-sql-server-database/connect-ssms.png)

3. Щелкните **Подключить**. После этого откроется окно обозревателя объектов. 

4. В обозревателе объектов разверните **базы данных**, затем выберите **myMigratedDatabase**, чтобы просмотреть объекты в образце базы данных.

## <a name="change-database-properties"></a>Изменение свойств базы данных

С помощью SQL Server Management Studio можно изменить уровень служб, производительности и совместимости.

1. В обозревателе объектов щелкните **myMigratedDatabase** правой кнопкой мыши и выберите **Создать запрос**. После этого откроется окно запроса, подключенное к базе данных.

2. Чтобы задать уровень служб **Стандартный** и уровень производительности **S1**.

    ```
    ALTER DATABASE myMigratedDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

    ![Изменение уровня служб](./media/sql-database-migrate-your-sql-server-database/service-tier.png)

3. Чтобы изменить уровень совместимости базы данных на **130**, выполните приведенную ниже команду.

    ```
    ALTER DATABASE myMigratedDatabase  
    SET COMPATIBILITY_LEVEL = 130;
    ```

   ![Изменение уровня совместимости](./media/sql-database-migrate-your-sql-server-database/compat-level.png)

## <a name="next-steps"></a>Дальнейшие действия 

- Общие сведения о переносе см. в статье [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).
- Сведения об отличиях Transact-SQL см. в статье [Отличия Transact-SQL базы данных SQL Azure](sql-database-transact-sql-information.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Visual Studio Code](sql-database-connect-query-vscode.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью .NET](sql-database-connect-query-dotnet.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью PHP](sql-database-connect-query-php.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Node.js](sql-database-connect-query-nodejs.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Java](sql-database-connect-query-java.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Python](sql-database-connect-query-python.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Ruby](sql-database-connect-query-ruby.md).


