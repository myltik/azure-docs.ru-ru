---
title: Перенос в Управляемый экземпляр Базы данных SQL Azure с помощью DMS | Документация Майкрософт
description: Узнайте, как выполнять миграцию из локального экземпляра SQL Server в Управляемый экземпляр Базы данных SQL Azure с помощью Azure Database Migration Service.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/07/2018
ms.openlocfilehash: bb7cc17c36809975e26c8da8beda004a0b0cfd9e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774154"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure с помощью DMS
Azure Database Migration Service можно использовать для переноса баз данных из локального экземпляра SQL Server в [Управляемый экземпляр базы данных SQL Azure](../sql-database/sql-database-managed-instance.md). Сведения о дополнительных методах, которые могут потребовать некоторых действий вручную, см. в статье [Перенос экземпляра SQL Server в Управляемый экземпляр базы данных SQL Azure](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Проекты миграции из SQL Server в Управляемый экземпляр базы данных SQL Azure находятся в стадии предварительной версии, и на них распространяются [Дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этом руководстве выполняется миграция базы данных **Adventureworks2012** из локального экземпляра SQL Server в Управляемый экземпляр базы данных SQL Azure с помощью Azure Database Migration Service.

Из этого руководства вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * мониторинг миграции.
> * скачивание отчета о миграции.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством вам потребуется следующее:

- Создайте виртуальную сеть для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Изучите [сетевые топологии для миграции Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Убедитесь, что правила группы безопасности сети для виртуальной сети Azure не блокируют порты связи 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Настройте [брандмауэр Windows для доступа к ядру исходной СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
- Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
- Если перед исходными базами данных используется брандмауэр, его правила должны разрешать службе Azure Database Migration Service доступ к исходным базам данных для миграции и к файлам SMB через порт 445.
- Создайте Управляемый экземпляр Базы данных SQL Azure на портале Azure, следуя инструкциям из [этой статьи](https://aka.ms/sqldbmi).
- Убедитесь, что для подключения к исходному серверу SQL Server и целевому управляемому экземпляру используются имена, входящие в серверную роль sysadmin.
- Создайте общую сетевую папку, которую Azure Database Migration Service сможет использовать для резервного копирования базы данных-источника.
- Предоставьте учетной записи службы, от имени которой выполняется исходный экземпляр SQL Server, права на запись в эту сетевую папку, а учетной записи компьютера для исходного сервера — права на чтение и запись для этой папки.
- Запишите имя пользователя и пароль учетной записи Windows, которой предоставлены полные права доступа к этой сетевой папке. Служба Azure Database Migration Service олицетворяет пользователя с этими учетными данными, чтобы отправить файлы резервных копий в контейнер службы хранилища Azure для операции восстановления.
- Создайте контейнер больших двоичных объектов и получите его URI SAS, следуя инструкциям, приведенным в разделе [Получение SAS для контейнера больших двоичных объектов](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), и не забудьте установить все разрешения (чтение, запись, удаление, перечисление) в окне политик при создании URI SAS. Это позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий, которые используются для переноса баз данных в Управляемый экземпляр базы данных SQL Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.

    ![Отображение подписок на портале](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

    ![Отображение поставщиков ресурсов](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Создание экземпляра Azure Database Migration Service

1. На портале Azure выберите + **Создать ресурс**, введите в поле поиска **Azure Database Migration Service**, а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. На экране **Azure Database Migration Service** выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. На экране **Создание службы миграции** укажите имя службы, подписку и новую или существующую группу ресурсов.

4. Выберите существующую виртуальную сеть или создайте новую.
 
    Виртуальная сеть предоставляет Azure Database Migration Service доступ к исходному экземпляру SQL Server и конечному Управляемому экземпляру базы данных SQL Azure.

    Дополнительные сведения о создании виртуальной сети на портале Azure см. в статье [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

    Подробные сведения см. в статье [Сетевые топологии для переноса Управляемого экземпляра базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

5. Выберите ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).
   
    ![Создание службы DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите ее на портале Azure, откройте и создайте проект миграции.

1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).

    ![Поиск всех экземпляров Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. На экране **Azure Database Migration Services** найдите имя созданного экземпляра и выберите его.
 
3. Выберите **+ Новый проект миграции**.

4. На экране **New migration project** (Новый проект миграции) задайте имя для проекта, в текстовом поле **Source server type** (Тип исходного сервера) выберите **SQL Server**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **Управляемый экземпляр Базы данных SQL Azure**.

   ![Создание проекта DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Выберите **Создать**, чтобы создать проект.

## <a name="specify-source-details"></a>Указание сведений об источнике

1. На экране **Сведения об источнике** задайте сведения о подключении для исходного SQL Server.

2. Если на сервере не установлен доверенный сертификат, установите флажок **Доверять сертификату сервера**.

    Если доверенный сертификат не установлен, SQL Server создает самозаверяющий сертификат при запуске экземпляра. Этот сертификат используется с целью шифрования учетных данных для клиентских подключений.

    > [!CAUTION]
    > SSL-соединения, шифруемые с помощью самозаверяющего сертификата, не обеспечивают надежной защиты. Они уязвимы для атак "злоумышленник в середине". В рабочей среде или на серверах, подключенных к Интернету, не следует применять самозаверяющие сертификаты для SSL.

   ![Сведения об источнике](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Щелкните **Сохранить**.

4. На экране **Выбор баз данных-источников** выберите базу данных **Adventureworks2012** для миграции.

   ![Выбор баз данных-источников](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Щелкните **Сохранить**.

## <a name="specify-target-details"></a>Указание сведений о цели

1.  На экране **Данные целевого объекта** задайте сведения о подключении для целевого объекта — предварительно подготовленного Управляемого экземпляра базы данных SQL Azure, в который будет перенесена база данных **AdventureWorks2012**.

    Если Управляемый экземпляр базы данных SQL Azure еще не подготовлен, выберите **Нет** и перейдите по ссылке, чтобы подготовить его. Вы можете продолжить создание проекта, а затем, когда Управляемый экземпляр базы данных SQL Azure будет готов, вернуться к этому проекту, чтобы выполнить миграцию.   
 
       ![Выбор цели](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Щелкните **Сохранить**.

3.  На экране **Сводка по проекту** просмотрите и проверьте сведения, связанные с проектом миграции.
 
    ![Сводка по проекту миграции](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Щелкните **Сохранить**.   

## <a name="run-the-migration"></a>Выполнение миграции

1.  Выберите недавно сохраненный проект, а затем щелкните **+ Новое действие** и выберите **Выполнить миграцию**.

    ![Создание нового действия](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  Когда появится соответствующий запрос, введите учетные данные исходного и целевого серверов, а затем выберите **Сохранить**.

3.  На экране **Выбор баз данных-источников** выберите базу данных-источник для миграции.

    ![Выбор баз данных-источников](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases1.png)

4.  Выберите **Сохранить**, а затем на экране **Настройка параметров миграции** укажите следующие сведения:

    | | |
    |--------|---------|
    |**Общая сетевая папка** | Локальная сетевая папка, в которую Azure Database Migration Service может поместить резервные копии базы данных-источника. Учетная запись службы, в которой выполняется исходный экземпляр SQL Server, должна иметь права записи для этой сетевой папки. Укажите полное доменное имя или IP-адрес сервера и сетевую папку, например "\\\servername.domainname.com\backupfolder" или "\\\IP address\backupfolder".|
    |**Имя пользователя** | Имя пользователя Windows, которое Azure Database Migration Service может использовать для олицетворения при отправке файлов резервных копий в контейнер хранилища Azure для операции восстановления. |
    |**Пароль** | Пароль для пользователя |
    |**Параметры учетной записи хранения** | Код URI SAS, который позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий, используемых для переноса базы данных в Управляемый экземпляр базы данных SQL Azure. [Получение SAS для контейнера больших двоичных объектов](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Настройка параметров миграции](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings1.png)

5.  Выберите **Сохранить**, а затем на экране **Сводка по миграции** в текстовом поле **Имя действия** задайте имя для действия миграции.

6. Разверните раздел **Вариант проверки**, чтобы открыть экран **Выбор варианта проверки**, укажите, нужно ли проверять переносимую базу данных на правильность запроса, а затем щелкните **Сохранить**.  

    ![Сводка по миграции](media\tutorial-sql-server-to-managed-instance\dms-migration-summary1.png)

7. Выберите **Запустить миграцию**.

    Появится окно действия миграции, в котором будет указано состояние действия **Ожидание**.

   ![Действие миграции в состоянии ожидания](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-pending.png)

## <a name="monitor-the-migration"></a>Мониторинг миграции

1. На экране действия миграции нажимайте кнопку **Обновить**, чтобы обновить содержимое экрана, пока состояние миграции не поменяется на **Завершено**.
 
   ![Действие миграции в состоянии "Завершено"](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-finished.png)

2. После завершения миграции нажмите **Скачать отчет**, чтобы получить отчет с подробными сведениями о процессе миграции.
 
3. Проверьте конечную базу данных в конечной среде Управляемого экземпляра базы данных SQL Azure.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о переносе базы данных в Управляемый экземпляр с помощью команды T-SQL RESTORE см. в [этой статье](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Сведения об Управляемом экземпляре см. в [обзоре Управляемого экземпляра](../sql-database/sql-database-managed-instance.md).
- Сведения о подключении приложений к Управляемому экземпляру см. в статье [Подключение приложения к Управляемому экземпляру Базы данных SQL](../sql-database/sql-database-managed-instance-connect-app.md).
