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
ms.custom: mvc,load & move data
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/27/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 375d3ea0230e7d3fd0fc02ca7e0b8a7a76c24a27
ms.contentlocale: ru-ru
ms.lasthandoff: 06/30/2017


---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Перенос базы данных SQL Server в базу данных SQL Azure

Перемещение базы данных SQL Server в базу данных SQL Azure происходит в три этапа — подготовка, экспорт и импорт базы данных. Из этого руководства вы узнаете, как выполнять такие задачи.

> [!div class="checklist"]
> * Подготовка базы данных в SQL Server для миграции в базу данных SQL Azure с помощью [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
> * Экспорт базы данных в BACPAC-файл.
> * Импорт BACPAC-файла в базу данных SQL Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством выполните следующие предварительные требования:

- Установите последнюю версию [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). При установке SSMS также устанавливается последняя версия SqlPackage, служебной программы командной строки, используемой для автоматизации ряда задач по разработке базы данных. 
- Установите [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- Вы должны определить базу данных, которую необходимо переместить, и иметь к ней доступ. В этом руководстве используется [база данных SQL Server 2008 R2 AdventureWorks OLTP](https://msftdbprodsamples.codeplex.com/releases/view/59211) в экземпляре SQL Server 2008 R2 или более поздней версии, но можно использовать любую базу данных по своему усмотрению. Чтобы устранить проблемы совместимости, используйте средства [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

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

8. Просмотрите результаты. Отображающиеся результаты **сравнения функций SQL Server** являются результатами по умолчанию, которые вы можете просмотреть. В частности, просмотрите сведения о неподдерживаемых и частично поддерживаемых функциях, а также сведения о рекомендуемых действиях. 

     ![Результаты сравнения при подготовке к переносу данных](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. Просмотрите **проблемы совместимости**, щелкнув соответствующий параметр в верхнем левом углу. Просмотрите сведения об ошибках переноса, изменениях в поведении и устаревших функциях для каждого уровня совместимости. Для базы данных AdventureWorks2008R2 просмотрите изменения в полнотекстовом поиске, начиная с SQL Server 2008, и изменения в SERVERPROPERTY('LCID'), начиная с SQL Server 2000. Дополнительные сведения об этих изменениях предоставлены по ссылкам. Многие параметры и настройки полнотекстового поиска изменились. 

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

После выполнения созданный BACPAC-файл сохраняется в каталоге, в котором находится исполняемый файл SqlPackage. В этом примере это C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin. 

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.

Войдите на [портал Azure](https://portal.azure.com/). Если войти в систему с компьютера, на котором вы используете служебную программу командной строки SqlPackage, это упростит создание правила брандмауэра на шаге 5.

## <a name="create-a-sql-server-logical-server"></a>Создание логического сервера SQL Server

[Логический сервер SQL Server](sql-database-features.md) выступает в качестве центральной точки администрирования нескольких баз данных. Чтобы создать логический сервер SQL Server для хранения перенесенной базы данных Adventure Works OLTP SQL Server, сделайте следующее: 

1. Щелкните **Создать** в верхнем левом углу портала Azure.

2. На странице **Создать** в окне поиска введите **sql server** и в отфильтрованном списке выберите **SQL database (logical server)** (База данных SQL (логический сервер)).

    ![выбор логического сервера](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. На странице **Все** щелкните **SQL server (логический сервер)** и на странице **SQL Server (логический сервер)** нажмите кнопку **Создать**.

    ![Создание логического сервера](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. Заполните форму "SQL Server (логический сервер)", указав следующую информацию, как показано на предыдущем изображении.     

   | Настройка       | Рекомендуемое значение | Описание | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Server name** (Имя сервера) | Введите любое глобально уникальное имя | Допустимые имена серверов см. в статье о [правилах и ограничениях именования](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
   | **Имя для входа администратора сервера** | Введите любое допустимое имя | Допустимые имена входа см. в статье об [идентификаторах базы данных](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Пароль** | Введите любой допустимый пароль | Длина пароля должна составлять минимум 8 символов. Пароль должен содержать символы трех категорий из перечисленных: прописные буквы, строчные буквы, цифры и символы, не являющиеся буквами или цифрами. |
   | **Подписка** | Выбор подписки | Дополнительные сведения о подписках см. [здесь](https://account.windowsazure.com/Subscriptions). |
   | **Группа ресурсов** | Выберите имеющуюся группу ресурсов или создайте ее, например **myResourceGroup** |  Допустимые имена групп ресурсов см. в статье о [правилах и ограничениях именования](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Расположение** | Введите любое допустимое расположение для нового сервера | Дополнительные сведения о регионах Azure см. [здесь](https://azure.microsoft.com/regions/). |

   ![Заполненная форма создания логического сервера](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. Чтобы подготовить логический сервер, нажмите кнопку **Создать**. Подготовка занимает несколько минут. 

> [!IMPORTANT]
> Запомните имя сервера, имя для входа администратора сервера и пароль. Эти значения вам понадобятся позже (в рамках этого руководства).
>

## <a name="create-a-server-level-firewall-rule"></a>создадим правило брандмауэра на уровне сервера;

Служба базы данных SQL создает [брандмауэр уровня сервера](sql-database-firewall-configure.md), который не позволяет внешним приложениям и средствам подключаться к серверу или любой базе данных на сервере, если не создано правило брандмауэра, открывающее брандмауэр для определенных IP-адресов. Чтобы создать правило брандмауэра уровня сервера базы данных SQL для IP-адреса компьютера, на котором вы запускаете служебную программу командной строки SqlPackage, выполните приведенные ниже действия. Это позволит SQLPackage подключиться к логическому серверу базы данных SQL Server через брандмауэр базы данных SQL Azure. 

1. В меню слева щелкните **Все ресурсы** и на открывшейся странице **Все ресурсы** выберите новый сервер. Откроется страница обзора сервера с параметрами для следующей настройки.

     ![Общие сведения о логическом сервере](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. На странице обзора на вкладке **Параметры** щелкните **Брандмауэр** в меню слева. Откроется страница **параметров брандмауэра** для сервера базы данных SQL. 

3. Щелкните **Добавить IP-адрес клиента** на панели инструментов, чтобы добавить IP-адрес используемого компьютера, а затем щелкните **Сохранить**. Для текущего IP-адреса будет создано правило брандмауэра уровня сервера.

     ![Настройка правила брандмауэра для сервера](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. Нажмите кнопку **ОК**.

Теперь можно подключиться ко всем базам данных на сервере с помощью SQL Server Management Studio или другого средства по своему усмотрению с этого IP-адреса, используя учетную запись администратора сервера, созданную ранее.

> [!NOTE]
> База данных SQL обменивается данными через порт 1433. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 1433 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу базы данных SQL Azure, пока ваш ИТ-отдел не откроет порт 1433.
>

## <a name="import-a-bacpac-file-to-azure-sql-database"></a>Импорт BACPAC-файла в базу данных SQL Azure 

Последние версии служебной программы командной строки SqlPackage поддерживают создание базы данных SQL Azure в соответствии с указанным [уровнем служб и производительности](sql-database-service-tiers.md). Чтобы повысить производительность импорта, выберите высокий уровень производительности и служб, а затем после импорта выполните масштабирование, если уровень производительности и служб выше требуемого.

Чтобы импортировать базу данных AdventureWorks2008R2 в базу данных SQL Azure с помощью служебной программы командной строки SqlPackage, выполните приведенные ниже действия. При использовании SQL Server Management Studio для выполнения этой задачи SQLPackage является предпочтительным методом для большинства рабочих сред, чтобы обеспечить максимальную гибкость и оптимальную производительность. Дополнительные сведения см. в записи блога [Migrating from SQL Server to Azure SQL Database using Bacpac Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Перемещение с сервера SQL Server в базу данных SQL Azure с помощью BACPAC-файлов).

- Выполните приведенную ниже команду SQLPackage в окне командной строки, чтобы импортировать базу данных **AdventureWorks2008R2** из локального хранилища на логический сервер SQL Server, созданный ранее с новой базой данных, уровнем служб **Премиум** и целью обслуживания **P6**. Замените значения в угловых скобках на соответствующие значения для логического сервера SQL Server, а также задайте имя для новой базы данных (также замените угловые скобки). Вы также можете изменить значения для выпуска базы данных и цели обслуживания в зависимости от среды. В рамках этого руководства выберем имя для перемещенной базы данных — **myMigratedDatabase**.

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=<your_server_name>.database.windows.net;Initial Catalog=<your_new_database_name>;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![Импорт SqlPackage](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> Логический сервер SQL Server прослушивает порт 1433. Чтобы успешно подключиться к логическому серверу SQL Server из среды, ограничиваемой корпоративным брандмауэром, этот порт должен быть открыт в этом корпоративном брандмауэре.
>

## <a name="connect-using-sql-server-management-studio-ssms"></a>Подключение с помощью SQL Server Management Studio

В рамках этого руководства используйте SQL Server Management Studio, чтобы подключиться к серверу базы данных SQL Azure и перемещенной базе данных с именем **myMigratedDatabase**. При запуске SQL Server Management Studio на другом компьютере (не на том, где была запущена SqlPackage) создайте правило брандмауэра для этого компьютера, следуя инструкциям в предыдущей процедуре.

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

С помощью SQL Server Management Studio можно изменить уровень служб, производительности и совместимости. Мы рекомендуем выполнять импорт в базу данных с высоким уровнем производительности для обеспечения оптимальной производительности. По завершении импорта вы можете уменьшить масштаб для сокращения расходов, пока вы не будете готовы активно использовать импортированную базу данных. При изменении уровня совместимости может повыситься производительность и станут доступны последние возможности службы базы данных SQL Azure. При перемещении старой базы данных сохраняется минимальный поддерживаемый уровень совместимости, совместимый с импортируемой базой данных. Дополнительные сведения см. в статье [Повышение производительности запросов с использованием уровня совместимости 130 в базе данных SQL Azure](sql-database-compatibility-level-query-performance-130.md).

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
В этом руководстве вы подготовили, экспортировали и импортировали базу данных. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Подготовка базы данных в SQL Server для миграции в базу данных SQL Azure.
> * Экспорт базы данных в BACPAC-файл.
> * Импорт BACPAC-файла в базу данных SQL Azure.

Перейдите к следующему руководству, чтобы узнать, как обеспечить защиту базы данных.

> [!div class="nextstepaction"]
> [Защита базы данных SQL Azure](sql-database-security-tutorial.md)



