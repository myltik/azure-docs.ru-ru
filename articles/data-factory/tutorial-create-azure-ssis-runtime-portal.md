---
title: "Подготовка среды выполнения интеграции Azure SSIS с помощью портала | Документация Майкрософт"
description: "В этой статье описывается создание среды выполнения интеграции Azure SSIS с помощью портала Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: 281fe65393086ec6a04dcba5aae868f4fec097ad
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>Подготовка среды выполнения интеграции Azure SSIS с помощью пользовательского интерфейса фабрики данных
В этом руководстве представлены шаги по подготовке с помощью портала Azure среды выполнения интеграции Azure SSIS в фабрике данных Azure. Затем можно использовать SQL Server Data Tools (SSDT) ​​или SQL Server Management Studio (SSMS) для развертывания пакетов служб SSIS для этой среды выполнения в Azure. См. дополнительные сведения о [среде выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Вот какие шаги выполняются в этом руководстве:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создание и запуск среды выполнения интеграции Azure SSIS

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>предварительным требованиям
- **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу. 
- **Сервер базы данных SQL Azure**. Если у вас еще нет сервера базы данных, создайте его на портале Azure перед началом работы. Фабрика данных Azure создает на этом сервере базу данных каталога служб SSIS (SSISDB). Мы рекомендуем создать сервер базы данных в одном регионе Azure со средой интеграции. Эта конфигурация позволяет среде выполнения интеграции записывать журналы выполнения в SSISDB, не пересекая регионы Azure. 
    - Убедитесь, что для сервера базы данных включен параметр **Разрешить доступ к службам Azure**. Дополнительные сведения см. в разделе [Создание правила брандмауэра на уровне сервера с помощью портала Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Сведения о включении этого параметра с помощью PowerShell см. в статье о [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Добавьте IP-адрес клиентского компьютера или диапазон IP-адресов, который включает IP-адрес клиентского компьютера, в список IP-адресов клиента в параметрах брандмауэра для сервера базы данных. Дополнительные сведения см. в разделе [Правила брандмауэра уровня сервера и уровня базы данных SQL Azure](../sql-database/sql-database-firewall-configure.md).
    - Убедитесь, что на сервере базы данных SQL Azure нет каталога SSIS (базы данных SSIDB). При подготовке среды Azure SSIS IR не поддерживается использование существующего каталога SSIS.
 
## <a name="create-a-data-factory"></a>Создание фабрики данных

1. Войдите на [портал Azure](https://portal.azure.com/).    
2. В меню слева щелкните **Создать**, выберите **Данные+аналитика** и щелкните **Фабрика данных**. 
   
   ![Создать -> Фабрика данных](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. На странице **Новая фабрика данных** введите **MyAzureSsisDataFactory** в поле **Имя**. 
      
     ![Страница "Новая фабрика данных"](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть **глобально уникальным**. Если вы получите указанную ниже ошибку, введите другое имя фабрики данных (например, ваше_имя_MyAzureSsisDataFactory) и попробуйте создать фабрику данных снова. Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](naming-rules.md), чтобы узнать правила именования для артефактов службы "Фабрика данных".
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Выберите **подписку** Azure, в рамках которой вы хотите создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий.
     
      - Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке. 
      - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
      Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
4. Выберите **V2 (Preview)** (V2 (предварительная версия)) для **версии**.
5. Укажите **расположение** фабрики данных. В списке отображаются только те расположения, в которых можно создать фабрики данных.
6. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
7. Нажмите кнопку **Создать**.
8. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Когда завершится создание, откроется страница **Фабрика данных**, как показано на рисунке ниже.
   
   ![Домашняя страница фабрики данных](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Щелкните **Создание и мониторинг**, чтобы открыть на отдельной вкладке пользовательский интерфейс фабрики данных. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Подготовка среды выполнения интеграции Azure SSIS

1. На странице начала работы щелкните плитку **Configure SSIS Integration Runtime** (Настройка среды выполнения интеграции SSIS). 

   ![Плитка настройки среды выполнения интеграции SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. На странице **General Settings** (Общие параметры) для **настройки среды выполнения интеграции** выполните следующие действия: 

   ![Общие параметры](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Укажите **имя** среды выполнения интеграции.
    2. Укажите **расположение** среды выполнения интеграции. Здесь отображаются только поддерживаемые расположения.
    3. Выберите **размер узла**, который нужно настроить с помощью среды выполнения интеграции SSIS.
    4. Укажите **количество узлов** в кластере.
    5. Нажмите кнопку **Далее**. 
1. В разделе **SQL Settings** (Параметры SQL) выполните следующие действия: 

    ![Раздел "SQL Settings" (Параметры SQL)](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Укажите **подписку** Azure, в которой размещен сервер Azure SQL. 
    2. Выберите нужный сервер Azure SQL в списке **Catalog Database Server Endpoint** (Конечная точка сервера базы данных каталога).
    3. Введите имя пользователя учетной записи **администратора**.
    4. Введите **пароль** администратора.  
    5. Выберите **уровень обслуживания** для базы данных SSISDB. По умолчанию используется уровень "Базовый".
    6. Нажмите кнопку **Далее**. 
1.  На странице **Advanced Settings** (Дополнительные параметры) выберите значение для параметра **Maximum Parallel Executions Per Node** (Максимальное количество параллельных выполнений на каждом узле).   

    ![Дополнительные параметры](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Этот шаг **не является обязательным**. Если у вас есть классическая виртуальная сеть, к которой вы хотите присоединить среду выполнения интеграции, выберите параметр **Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings** (Выбрать виртуальную сеть для присоединения среды выполнения интеграции Azure SSIS и разрешить службам Azure настроить параметры и разрешения для виртуальной сети) и выполните следующие действия. 

    ![Расширенные параметры для виртуальной сети](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Укажите **подписку**, в которой размещена классическая виртуальная сеть. 
    2. Выберите **виртуальную сеть**. <br/>
    4. Выберите **подсеть**.<br/> 
1. Щелкните **Finish** (Готово), чтобы начать создание среды выполнения интеграции Azure SSIS. 

    > [!IMPORTANT]
    > - Эта процедура занимает около 20 минут.
    > - Служба фабрики данных подключается к базе данных SQL Azure для подготовки базы данных каталога SSIS. Скрипт также настраивает разрешения и параметры виртуальной сети, если это указано, и подключает к ней новый экземпляр среды выполнения интеграции Azure SSIS.
    > - При подготовке экземпляра базы данных SQL для размещения SSISDB также устанавливается пакет функций Azure для служб SSIS и распространяемый компонент Access. Эти компоненты обеспечивают подключение к файлам Excel и Access и к другим источникам данных Azure, кроме тех, которые поддерживаются встроенными компонентами. Сейчас вы не можете установить сторонние компоненты для служб SSIS (в том числе такие сторонние компоненты от Майкрософт, как компоненты Oracle и Teradata от Attunity и SAP BI).

7. В окне **Connections** (Подключения) перейдите на **среду выполнения интеграции**. Чтобы обновить состояние, щелкните **Refresh** (Обновить). 

    ![Состояние создания](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. С помощью ссылок в столбце **Actions** (Действия) вы можете остановить, запустить, изменить или удалить среду выполнения интеграции, а также начать для нее мониторинг. Последняя из этих ссылок позволяет просмотреть код JSON для среды выполнения интеграции. Кнопки изменения и удаления действуют только в том случае, если среда выполнения интеграции остановлена. 

    ![Среда выполнения интеграции Azure SSIS — действия](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. Щелкните ссылку **Monitor** (Мониторинг) в столбце **Actions** (Действия).  

    ![Среда выполнения интеграции Azure SSIS — подробные сведения](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. Если возникнет **ошибка**, связанная со средой выполнения интеграции Azure SSIS, на этой странице вы увидите число ошибок и ссылки для просмотра сведений о них. Например, если на выбранном сервере базы данных уже существует каталог служб SSIS, вы увидите ошибку с сообщением о том, что база данных SSISDB уже существует.  
11. Щелкните **Integration Runtimes** (Среды выполнения интеграции) в верхней части страницы, чтобы вернуться на предыдущую страницу для просмотра списка сред выполнения интеграции, связанных с этой фабрикой данных.  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Среда выполнения интеграции Azure SSIS на портале

1. В пользовательском интерфейсе фабрики данных Azure перейдите на вкладку **Изменить**, щелкните **Подключения**, а затем откройте вкладку **Integration Runtimes** (Среды выполнения интеграции), чтобы просмотреть все существующие в фабрике данных среды выполнения интеграции. 
    ![Просмотр существующих сред выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Щелкните **New** (Создать), чтобы создать новую среду выполнения интеграции Azure SSIS. 

    ![Доступ к среде выполнения интеграции через меню](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Чтобы создать среду выполнения интеграции Azure SSIS, щелкните действие **New** (Создать), как показано на рисунке. 
3. В окне настройки среды выполнения интеграции выберите **Lift-and-shift existing SSIS packages to execute in Azure** (Перенос существующих пакетов служб SSIS по методике lift-and-shift для выполнения в Azure), а затем щелкните **Next** (Далее).

    ![Указание типа среды выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Остальные шаги описаны в разделе [Подготовка среды выполнения интеграции Azure SSIS](#provision-an-azure-ssis-integration-runtime). 

 
## <a name="deploy-ssis-packages"></a>Развертывание пакетов служб SSIS.
Теперь используйте SQL Server Data Tools (SSDT) или SQL Server Management Studio (SSMS) для развертывания пакетов служб SSIS в Azure. Подключитесь к серверу Azure SQL, на котором размещен каталог служб SSIS (SSISDB). Имя сервера Azure SQL Server имеет формат `<servername>.database.windows.net` (для базы данных SQL Azure). 

См. документацию SSIS в следующих статьях: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (Развертывание, запуск и отслеживание пакета SSIS в Azure)   
- [Connect to the SSISDB Catalog database on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Подключение к базе данных каталога SSISDB в Azure)
- [Schedule the execution of an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Планирование выполнения пакета SSIS в Azure)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Подключение к локальным источникам данных с помощью аутентификации Windows) 

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи: 

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Создание и запуск среды выполнения интеграции Azure SSIS

Перейдите к следующему руководству, чтобы узнать о копировании данных из локальной среды в облако: 

> [!div class="nextstepaction"]
>[Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL Azure с помощью фабрики данных Azure](tutorial-copy-data-portal.md)
