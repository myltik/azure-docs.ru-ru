---
title: Использование Azure Database Migration Service для переноса SQL Server в базу данных SQL Azure | Документация Майкрософт
description: Узнайте, как выполнять миграцию из локального экземпляра SQL Server в SQL Azure с помощью Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/29/2018
ms.openlocfilehash: 48f1b3715f300fea7bfc0590e6d2e6c6622e83aa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>Миграция с SQL Server в базу данных SQL Azure
Azure Database Migration Service можно использовать для переноса баз данных из локального экземпляра SQL Server в базу данных Azure. В этом руководстве выполняется миграция базы данных **Adventureworks2012**, восстановленной на локальном экземпляре SQL Server 2016 (или более поздней версии), в базу данных SQL Azure с помощью Azure Database Migration Service.

Из этого руководства вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * получение доступа к локальной базе данных с помощью помощника по миграции данных;
> * перенос примера схемы с помощью помощника по миграции данных;
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции с помощью Azure Database Migration Service;
> * выполнение миграции.
> * мониторинг миграции.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством вам потребуется следующее:

- Скачайте и установите [SQL Server 2016 или более поздней версии](https://www.microsoft.com/sql-server/sql-server-downloads) (любой выпуск).
- При установке SQL Server Express протокол TCP/IP отключен по умолчанию. Включите его, выполнив инструкции в статье [Включение или отключение сетевого протокола сервера](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Создайте экземпляр базы данных SQL Azure, следуя инструкциям в статье [Создание базы данных SQL Azure на портале Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Скачайте и установите [Помощник по миграции данных](https://www.microsoft.com/download/details.aspx?id=53595) версии 3.3 или более поздней.
- Создайте виртуальную сеть для Azure Database Migration Service с помощью модели развертывания Azure Resource Manager, которая обеспечивает подключение "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Убедитесь, что правила группы безопасности сети для виртуальной сети Azure не блокируют порты связи 443, 53, 9354, 445 и 12000. Дополнительные сведения о фильтрации трафика, предназначенного для виртуальной сети Azure, с помощью NSG см. в статье [Фильтрация сетевого трафика с помощью групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Настройте [брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Откройте брандмауэр Windows, чтобы предоставить Azure Database Migration Service доступ к исходному серверу SQL Server. По умолчанию это TCP-порт 1433.
- Если вы запустили несколько именованных экземпляров SQL Server, использующих динамические порты, вы можете включить службу обозревателя SQL и разрешить доступ к UDP-порту 1434 через брандмауэры. Это позволит службе Azure Database Migration Service подключиться к именованному экземпляру на исходном сервере.
- Если перед исходными базами данных развернуто устройство брандмауэра, вам может понадобиться добавить правила брандмауэра, чтобы позволить службе Azure Database Migration Service обращаться к исходным базам данных для выполнения миграции.
- Создайте [правило брандмауэра](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) уровня сервера для сервера базы данных SQL Azure, чтобы предоставить службе Azure Database Migration Service доступ к целевым базам данных. Задайте диапазон подсети в виртуальной сети, которая используется для Azure Database Migration Service.
- Убедитесь, что учетные данные, используемые для подключения к исходному экземпляру SQL Server, имеют разрешения [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Убедитесь, что учетные данные, используемые для подключения к целевому экземпляру базы данных SQL Azure, имеют разрешения CONTROL DATABASE в целевых базах данных SQL Azure.

## <a name="assess-your-on-premises-database"></a>Оценка локальной базы данных
Перед переносом данных из локального экземпляра SQL Server в базу данных SQL Azure необходимо оценить базу данных SQL Server на наличие любых проблем, связанных с блокировкой, которые могут помешать миграции. В Помощнике по миграции данных версии 3.3 или выше выполните инструкции по [оценке миграции SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem), чтобы завершить оценку локальной базы данных. Ниже приведена сводка необходимых действий:
1.  В помощнике по миграции данных выберите значок "Создать" (+), а затем выберите тип проекта **Оценка**.
2.  Укажите имя проекта в текстовом поле **Source server type** (Тип исходного сервера), выберите **SQL Server**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **База данных SQL Azure**.
3.  Выберите **Создать**, чтобы создать проект.

    При оценке исходной базы данных SQL Server, переносимой в базу данных SQL Azure, можно выбрать один или несколько следующих типов отчетов об оценке:
    - проверка совместимости базы данных;
    - проверка четности компонентов.

    По умолчанию выбраны оба типа отчетов.
4.  В помощнике по миграции данных на экране **Параметры** выберите **Далее**.
5.  На экране **Выберите источники** в диалоговом окне **Соединение с сервером** предоставьте сведения о подключении к SQL Server, а затем выберите **Подключить**.
6.  В диалоговом окне **Add sources** (Добавление источников) выберите **AdventureWorks2012**, а затем щелкните **Add** (Добавить) и выберите **Start Assessment** (Начать оценку).

    После завершения оценки результаты будут показаны, как на следующем рисунке:

    ![Оценка миграции данных](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    В базе данных SQL Azure внутренняя оценка идентифицирует проблемы, блокирующие миграцию, и проблемы с четностью компонентов.

7.  Просмотрите результаты оценки проблем, блокирующих миграцию, и проблем с четностью компонентов, выбрав конкретные параметры.
    - Для категории **четности компонентов SQL Server** доступны разные рекомендации, а также описание альтернативных механизмы, используемых в Azure, и мер по устранению, которые помогут вам рассчитать трудозатраты для выполнения миграции.
    - Категория **проблем совместимости** определяет частично поддерживаемые или неподдерживаемые возможности, связанные с проблемами совместимости, которые могут блокировать миграцию локальных баз данных SQL Server в базу данных SQL Azure. Также предлагаются рекомендации, которые помогут вам решить эти проблемы.


## <a name="migrate-the-sample-schema"></a>Перенос примера схемы
Если результаты оценки вас устраивают, и выбранная база данных подходит для миграции в базу данных SQL Azure, воспользуйтесь Data Migration Assistant для переноса схемы в базу данных SQL Azure.

> [!NOTE]
> Прежде чем создавать проект миграции в Data Migration Assistant, убедитесь, что база данных SQL Azure уже подготовлена, как описано выше. В рамках этого руководства для базы данных SQL Azure используется имя **AdventureWorks2012**, но вы можете назвать ее иначе.

Чтобы перенести схему **AdventureWorks2012** в базу данных SQL Azure, выполните следующие действия:

1.  В Помощнике по миграции данных щелкните значок New (+) (Создать (+)), а затем в разделе **Project type** (Тип проекта) выберите **Migration** (Миграция).
3.  Укажите имя проекта в текстовом поле **Source server type** (Тип исходного сервера), выберите **SQL Server**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **База данных SQL Azure**.
4.  В разделе **Migration Scope** (Область переноса) выберите **Schema only** (Только схема).

    После выполнения предыдущих действий интерфейс помощника по миграции данных должен выглядеть, как показано на следующем рисунке:
    
    ![Создание проекта в помощнике по миграции данных](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  Выберите **Создать**, чтобы создать проект.
6.  В помощнике по миграции данных задайте сведения о подключении к источнику SQL Server, щелкните **Подключение**, а затем выберите базу данных **AdventureWorks2012**.

    ![Сведения о подключении к источнику в помощнике по миграции данных](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  В разделе **Connect to target server** (Подключение к целевому серверу) нажмите кнопку **Next** (Далее), укажите сведения о подключении к целевому объекту для базы данных SQL Azure, щелкните **Connect** (Подключение), а затем выберите базу данных **AdventureWorksAzure**, предварительно подготовленную в базе данных SQL Azure.

    ![Сведения о подключении к целевому объекту в помощнике по миграции данных](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  Выберите **Далее**, чтобы перейти на экран **Выбор объектов**, на котором можно указать объекты схемы в базе данных **AdventureWorks2012**, которую нужно развернуть в базе данных SQL Azure.

    По умолчанию выбраны все объекты.

    ![Создание сценариев SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  Выберите **Создать скрипт SQL**, чтобы создать скрипт SQL, а затем просмотрите его на наличие ошибок.

    ![Скрипт схемы](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. Выберите **Deploy schema** (Развернуть схему), чтобы развернуть схему в базу данных SQL Azure, а затем проверьте целевой сервер на наличие аномалий.

    ![Развертывание схемы](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Регистрация поставщика ресурсов Microsoft.DataMigration
1. Войдите на портал Azure, щелкните **Все службы** и выберите **Подписки**.
 
   ![Отображение подписок на портале](media\tutorial-sql-server-to-azure-sql\portal-select-subscription.png)
       
2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.
 
    ![Отображение поставщиков ресурсов](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)    
3.  В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.
 
    ![Регистрация поставщика ресурсов](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    


## <a name="create-an-instance"></a>Создание экземпляра
1.  На портале Azure выберите **+Создать ресурс**, введите в поле поиска Azure Database Migration Service, а затем в раскрывающемся списке выберите **Azure Database Migration Service**.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  На экране **Azure Database Migration Service (preview)** (Azure Database Migration Service (предварительная версия)) выберите **Создать**.
 
    ![Создание экземпляра Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  На экране **Database Migration Service** задайте имя службы, подписки, виртуальной сети и ценовой категории.

    Дополнительные сведения о ценовых категориях и затратах см. на [странице с описанием цен](https://aka.ms/dms-pricing).

     ![Настройка параметров экземпляра Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  Выберите **Создать**, чтобы создать службу.

## <a name="create-a-migration-project"></a>Создание проекта миграции
После создания службы найдите ее на портале Azure и создайте проект миграции.
1. На портале Azure щелкните **Все службы**, выполните поиск по запросу "Azure Database Migration Service" и выберите **Azure Database Migration Services** (Службы Azure Database Migration Service).
 
      ![Поиск всех экземпляров Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. На экране **Azure Database Migration Services** (Службы Azure Database Migration Service) найдите имя созданного экземпляра Azure DMS и выберите его.
 
     ![Поиск экземпляра службы миграции базы данных Azure](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Выберите **+ New Migration Project** (+ Новый проект миграции).
4. На экране **New migration project** (Новый проект миграции) задайте имя для проекта, в текстовом поле **Source server type** (Тип исходного сервера) выберите **SQL Server**, а затем в текстовом поле **Target server type** (Тип целевого сервера) выберите **База данных SQL Azure**.

    ![Создание проекта Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  Выберите **Создать**, чтобы создать проект.


## <a name="specify-source-details"></a>Указание сведений об источнике
1. На экране **Сведения об источнике** задайте сведения о подключении для исходного SQL Server.

    ![Выбор источника](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. Выберите **Сохранить**, а затем щелкните базу данных **AdventureWorks2012** для миграции.

    ![Выбор исходной базы данных](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>Указание сведений о цели
1. Выберите **Сохранить**, а затем на экране **Данные целевого объекта** задайте сведения о подключении для целевого объекта — предварительно подготовленной базы данных SQL Azure, в которую была развернута схема **AdventureWorks2012** с помощью Data Migration Assistant.

    ![Выбор цели](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. Выберите **Сохранить**, чтобы сохранить проект.
3. На экране **Migration summary** (Сводка миграции) просмотрите и проверьте сведения, связанные с проектом миграции.

    ![Сводка DMS](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. Щелкните **Сохранить**.

## <a name="run-the-migration"></a>Выполнение миграции
1.  Выберите недавно сохраненный проект, а затем щелкните **+ New Activity** (+ Новое действие) и выберите **Run Data Migration** (Выполнить перенос данных).

    ![Новое действие](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  При появлении запроса введите учетные данные исходного и целевого сервера, а затем выберите **Сохранить**.
3.  На экране **Map to target databases** (Сопоставить с целевыми базами данных) сопоставьте исходную и целевую базы данных для миграции.

    Если в целевой базе данных содержится такое же имя базы данных, что и в исходной базе данных, Azure DMS выберет целевую базу данных по умолчанию.

    ![Сопоставление с целевыми базами данных](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. На экране **Выбрать таблицы** выберите **Сохранить**, разверните список таблиц и просмотрите список затронутых полей.

    ![Выбор таблиц](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  На экране **Migration summary** (Сводка по миграции) выберите **Сохранить**, а в текстовом поле **Имя действия** задайте имя для действия миграции.

    На этом экране можно развернуть экран **Choose validation option** (Выбор варианта проверки), где можно выбрать категорию проверки переносимой базы данных:
    - Схема
    - согласованность данных;
    - правильность запроса и производительность.

    ![Выбор варианта проверки](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  Выберите **Сохранить**, просмотрите сводку, чтобы убедиться, что сведения об источнике и целевом объекте соответствуют предварительно заданным.

    ![Сводка по миграции](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  Выберите **Запуск переноса**, чтобы запустить действие миграции, а затем выберите **Обновить**, чтобы просмотреть текущее состояние.

    ![Состояние действия](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>Мониторинг миграции
1. Выберите действие миграции, чтобы просмотреть состояние действия.
2. Проверьте целевую базу данных SQL Azure после завершения миграции.

    ![Завершено](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
