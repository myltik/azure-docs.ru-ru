---
title: Использование Azure Database Migration Service для переноса SQL Server в Управляемый экземпляр Базы данных SQL Azure | Документация Майкрософт
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
ms.date: 03/29/2018
ms.openlocfilehash: 8abf3bae3a2274ed5514a5c621675b4c9ec27ae2
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance"></a>Перенос SQL Server в Управляемый экземпляр Базы данных SQL Azure
Azure Database Migration Service можно использовать для переноса баз данных из локального экземпляра SQL Server в базу данных Azure. В этом руководстве выполняется миграция базы данных **Adventureworks2012** из локального экземпляра SQL Server в Базу данных SQL Azure с помощью Azure Database Migration Service.

Из этого руководства вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * мониторинг миграции.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством вам потребуется следующее:

- Создайте виртуальную сеть для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Изучите [сетевые топологии для миграции Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Убедитесь, что правила группы безопасности сети для виртуальной сети Azure не блокируют порты связи 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Настройте [брандмауэр Windows для доступа к ядру исходной СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
- Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
- Если перед исходными базами данных используется брандмауэр, его правила должны разрешать службе Azure Database Migration Service доступ к исходным базам данных для миграции и к файлам SMB через порт 445.
- Создайте Управляемый экземпляр Базы данных SQL Azure, следуя инструкциям из статьи [Создание управляемого экземпляра базы данных SQL Azure на портале Azure](https://aka.ms/sqldbmi).
- Убедитесь, что для подключения к исходному серверу SQL Server и целевому управляемому экземпляру используются имена, входящие в серверную роль sysadmin.
- Создайте общую сетевую папку, которую Azure Database Migration Service сможет использовать для резервного копирования базы данных-источника.
- Предоставьте учетной записи службы, от имени которой выполняется исходный экземпляр SQL Server, права на запись для этой сетевой папки.
- Запишите имя пользователя и пароль учетной записи Windows, которой предоставлены полные права доступа к этой сетевой папке. Служба Azure Database Migration Service олицетворяет пользователя с этими учетными данными, чтобы отправить файлы резервных копий в контейнер службы хранилища Azure для операции восстановления.
- Создайте контейнер больших двоичных объектов и получите его URI SAS, следуя инструкциям, приведенным в статье [Управление ресурсами хранилища BLOB-объектов Azure с помощью обозревателя хранилищ (предварительная версия)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), и не забудьте установить все разрешения (чтение, запись, удаление, список) в окне политик при создании URI SAS. Это позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий, которые используются для переноса базы данных в Управляемый экземпляр Базы данных Azure SQL.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration

1.  Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.
![Отображение подписок на портале](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.
![Отображение поставщиков ресурсов](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.
![Регистрация поставщика ресурсов](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Создание экземпляра

1.  На портале Azure выберите **+Создать ресурс**, введите в поле поиска **Azure Database Migration Service**, а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  На экране **Azure Database Migration Service (preview)** (Azure Database Migration Service (предварительная версия)) выберите **Создать**.

    ![Создание экземпляра Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  На экране **Database Migration Service** укажите имя службы, подписку, группу ресурсов, виртуальную сеть и ценовую категорию.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing). *Azure Database Migration Service в настоящее время находится в предварительной версии и предоставляется бесплатно.*

    **Сеть:** выберите существующую виртуальную сеть или создайте новую, чтобы предоставить Azure Database Migration Service доступ к исходному экземпляру SQL Server и целевому Управляемому экземпляру Базы данных Azure SQL. Изучите [сетевые топологии для миграции Управляемого экземпляра Базы данных Azure SQL с помощью Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

    Дополнительные сведения о создании виртуальной сети на портале Azure вы найдете в статье [Создание виртуальной сети с помощью портала Azure](https://aka.ms/DMSVnet).

    ![Создание службы DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  Выберите **Создать**, чтобы создать службу.


## <a name="create-a-migration-project"></a>Создание проекта миграции

После создания службы найдите и откройте ее на портале Azure.

1.  Выберите **+ New Migration Project** (+ Новый проект миграции).

1.  На экране **New migration project** (Новый проект миграции) задайте имя для проекта, в текстовом поле **Source server type** (Тип исходного сервера) выберите **SQL Server**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **Управляемый экземпляр Базы данных SQL Azure**.

    ![Создание проекта DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  Выберите **Создать**, чтобы создать проект.

## <a name="specify-source-details"></a>Указание сведений об источнике

1.  На экране **Сведения об источнике** задайте сведения о подключении для исходного SQL Server.

    ![Сведения об источнике](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  Выберите **Сохранить**, а затем щелкните базу данных **Adventureworks2012** для миграции.

    ![Выбор баз данных-источников](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>Указание сведений о цели

1.  Выберите **Сохранить**, а затем на экране **Данные целевого объекта** задайте сведения о подключении для целевого объекта — предварительно подготовленный Управляемый объект Базы данных SQL Azure, в которую выполняется миграция **AdventureWorks2012**.

    ![Выбор цели](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  Щелкните **Сохранить**.

1.  На экране **Сводка по проекту** просмотрите и проверьте сведения, связанные с проектом миграции.

## <a name="run-the-migration"></a>Выполнение миграции

1.  Выберите недавно сохраненный проект, а затем щелкните **+ New Activity** (+ Новое действие) и выберите **Run Migration** (Выполнить миграцию).

    ![Создание нового действия](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  Когда появится соответствующий запрос, введите учетные данные исходного и целевого серверов, а затем выберите **Сохранить**.

1.  На экране **Map to target databases** (Сопоставление с целевыми базами данных) выберите для миграции одну или несколько баз данных-источников.

    ![Выбор баз данных-источников](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  Выберите **Сохранить** на экране **Configure migration settings** (Настройка параметров миграции) и укажите следующие сведения:

    | | |
    |--------|---------|
    |**Расположение резервной копии сервера** | Локальная сетевая папка, в которую Azure Database Migration Service может поместить резервные копии базы данных-источника. Учетная запись службы, в которой выполняется исходный экземпляр SQL Server, должна иметь права записи для этой сетевой папки. |
    |**Имя пользователя** | Имя пользователя Windows, которое Azure Database Migration Service может использовать для олицетворения при отправке файлов резервных копий в контейнер хранилища Azure для операции восстановления. |
    |**Пароль** | Пароль указанного выше пользователя. |
    |**URI SAS хранилища** | URI SAS, который позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий, которые используются для переноса базы данных в Управляемый экземпляр Базы данных Azure SQL. [Получение SAS для контейнера больших двоичных объектов](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Настройка параметров миграции](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  На экране "Сводка по миграции" выберите **Сохранить**, а в текстовом поле **Имя действия** задайте имя для действия миграции.

    ![Сводка по миграции](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>Мониторинг миграции

1.  Выберите действие миграции, чтобы просмотреть состояние действия.

1.  После завершения миграции проверьте целевые базы данных в целевом Управляемом экземпляре Базы данных Azure SQL.

    ![Мониторинг миграции](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

