---
title: Развертывание пакетов служб SSIS в Azure | Документация Майкрософт
description: В этой статье объясняется, как развертывать пакеты служб SSIS в Azure и создать среду выполнения интеграции Azure-SSIS с использованием фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: hero-article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: cc0c26d83794cfb0b398e668ae89e268901df345
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure
В этом руководстве представлены шаги по подготовке с помощью портала Azure среды выполнения интеграции Azure SSIS в фабрике данных Azure. Затем можно использовать SQL Server Data Tools (SSDT) ​​или SQL Server Management Studio (SSMS) для развертывания пакетов служб SSIS для этой среды выполнения в Azure. См. дополнительные сведения о [среде выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

В этом руководстве выполняются следующие шаги:

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Подготовка среды выполнения интеграции Azure SSIS

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь со статьей [Руководство. Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>предварительным требованиям
- **Подписка Azure**. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу. 
- **Сервер базы данных SQL Azure**. Если у вас еще нет сервера базы данных, создайте его на портале Azure перед началом работы. Фабрика данных Azure создает на этом сервере каталог служб SSIS (базу данных SSISDB). Мы рекомендуем создать сервер базы данных в одном регионе Azure со средой интеграции. Эта конфигурация позволяет среде выполнения интеграции записывать журналы выполнения в базе данных SSISDB, не пересекая регионы Azure. 
- Убедитесь, что для сервера базы данных включен параметр **Разрешить доступ к службам Azure**. Дополнительные сведения см. в разделе [Создание правила брандмауэра на уровне сервера с помощью портала Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Сведения о включении этого параметра с помощью PowerShell см. в статье о [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
- Добавьте IP-адрес клиентского компьютера или диапазон IP-адресов, который включает IP-адрес клиентского компьютера, в список IP-адресов клиента в параметрах брандмауэра для сервера базы данных. Дополнительные сведения см. в разделе [Правила брандмауэра уровня сервера и уровня базы данных SQL Azure](../sql-database/sql-database-firewall-configure.md).
- Убедитесь, что на сервере базы данных SQL Azure нет каталога SSIS (базы данных SSISDB). При подготовке среды Azure SSIS IR не поддерживается использование существующего каталога SSIS.

> [!NOTE]
> - Вы можете создать фабрику данных версии 2 в следующих регионах: восточная часть США, восточная часть США 2, Юго-Восточная Азия и Западная Европа. 
> - Среду IR Azure-SSIS можно создать в следующих регионах: восточная часть США, восточная часть США 2, центральная часть США, западная часть США 2, Северная Европа, Западная Европа, южная часть Соединенного Королевства и Восточная Австралия. 

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. Запустите веб-браузер **Microsoft Edge** или **Google Chrome**. Сейчас только эти браузеры поддерживают пользовательский интерфейс фабрики данных.
2. Войдите на [портале Azure](https://portal.azure.com/).    
3. В меню слева выберите **Создать**, **Данные+аналитика**, **Фабрика данных**. 
   
   ![Выбор фабрики данных в области "Создать"](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. На странице **Новая фабрика данных** введите **MyAzureSsisDataFactory** в поле **Имя**. 
      
   ![Страница "Новая фабрика данных"](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть *глобально уникальным*. Если вы получите указанную ниже ошибку, введите другое имя фабрики данных (например, **&lt;ваше_имя&gt;MyAzureSsisDataFactory**) и попробуйте создать фабрику данных снова. Правила именования для артефактов службы "Фабрика данных" см. в [этой](naming-rules.md) статье.
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. В поле **Подписка** выберите подписку Azure, в рамках которой вы хотите создать фабрику данных. 
5. Для **группы ресурсов** выполните одно из следующих действий:
     
   - Выберите **Use existing** (Использовать имеющуюся) и выберите имеющуюся группу ресурсов в списке. 
   - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
   Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
6. Для **версии** выберите **V2 (предварительная версия)**.
7. В поле **Расположение** выберите расположение фабрики данных. В списке отображаются только те расположения, в которых можно создать фабрики данных.
8. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
9. Нажмите кнопку **Создать**.
10. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных): 

   ![Элемент Deploying data factory (Развертывание фабрики данных)](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. Когда создание завершится, откроется страница **Фабрика данных**.
   
   ![Домашняя страница фабрики данных](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. Выберите **Мониторинг и управление**, чтобы открыть на отдельной вкладке пользовательский интерфейс фабрики данных. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Подготовка среды выполнения интеграции Azure SSIS

1. На странице **начала работы** щелкните плитку **Configure SSIS Integration Runtime** (Настройка среды выполнения интеграции SSIS). 

   ![Плитка настройки среды выполнения интеграции SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. На странице **General Settings** (Общие параметры) для **настройки среды выполнения интеграции** выполните следующие действия: 

   ![Общие параметры](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. В поле **Имя** укажите имя среды выполнения интеграции.

   Б. В поле **Расположение** укажите расположение среды выполнения интеграции. Здесь отображаются только поддерживаемые расположения.

   c. В поле **Размер узла** укажите размер узла, который нужно настроить с помощью среды выполнения интеграции SSIS.

   d. В поле **Node Number** (Количество узлов) укажите количество узлов в кластере.
   
   д. Щелкните **Далее**. 
3. На странице **SQL Settings** (Параметры SQL) сделайте следующее: 

   ![Раздел "SQL Settings" (Параметры SQL)](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. В поле **Подписка** укажите подписку Azure, в которой размещен сервер базы данных Azure.

   Б. Выберите нужный сервер базы данных Azure в списке **Catalog Database Server Endpoint** (Конечная точка сервера базы данных каталога).

   c. В поле **Имя администратора** введите имя администратора домена.

   d. В поле **Admin Password** (Пароль администратора) введите пароль для администратора домена.

   д. В поле **Catalog Database Server Tier** (Каталог уровней сервера базы данных) выберите уровень службы для базы данных SSISDB. По умолчанию используется уровень **Базовый**.

   f. Щелкните **Далее**. 
4. На странице **Advanced Settings** (Дополнительные параметры) выберите значение для параметра **Maximum Parallel Executions Per Node** (Максимальное количество параллельных выполнений на каждом узле).   

   ![Дополнительные параметры](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Этот шаг *не является обязательным*. Если у вас есть виртуальная сеть (созданная с использованием классической модели развертывания или Azure Resource Manager), к которой вы хотите присоединить среду выполнения интеграции, установите флажок **Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings** (Выбрать виртуальную сеть для присоединения среды выполнения интеграции Azure SSIS и разрешить службам Azure настроить параметры и разрешения для виртуальной сети). Затем сделайте следующее: 

   ![Дополнительные параметры с использованием виртуальной сети](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. В поле **Подписка** укажите подписку, в которой размещена виртуальная сеть.

   Б. В поле **Имя виртуальной сети** выберите имя виртуальной сети.

   c. В поле **Имя подсети** выберите имя подсети в виртуальной сети. 
6. Выберите **Готово**, чтобы начать создание среды выполнения интеграции Azure SSIS. 

   > [!IMPORTANT]
   > Эта процедура занимает около 20 минут.
   >
   > Служба фабрики данных подключается к базе данных SQL Azure для подготовки каталога SSIS (базы данных SSISDB). Этот скрипт также настраивает разрешения и параметры для виртуальной сети, если они указаны, а также присоединяет новый экземпляр среды выполнения интеграции Azure-SSIS к виртуальной сети.
   > 
   > При подготовке экземпляра среды выполнения интеграции Azure-SSIS также устанавливается пакет функций Azure для служб SSIS и распространяемый компонент Access. Эти компоненты обеспечивают подключение к файлам Excel и Access и другим источникам данных Azure, кроме тех, которые поддерживаются встроенными компонентами. Кроме того, вы можете установить дополнительные компоненты. Дополнительные сведения см. в статье о [настройке среды выполнения интеграции Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

7. На вкладке **Подключения** перейдите к **среде выполнения интеграции**. Щелкните **Обновить**, чтобы обновить состояние. 

   ![Состояние создания с кнопкой "Обновить"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. С помощью ссылок в столбце **Действия** вы можете остановить, запустить, изменить или удалить среду выполнения интеграции. Последняя из этих ссылок позволяет просмотреть код JSON для среды выполнения интеграции. Кнопки изменения и удаления действуют только в том случае, если среда выполнения интеграции остановлена. 

   ![Связи в столбце "Действия"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>Создание среды выполнения интеграции Azure SSIS.

1. В пользовательском интерфейсе фабрики данных Azure перейдите на вкладку **Изменить**, выберите **Подключения**, а затем откройте вкладку **Integration Runtimes** (Среды выполнения интеграции), чтобы просмотреть все имеющиеся в фабрике данных среды выполнения интеграции. 
   ![Выделения для просмотра имеющихся сред выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. Выберите **Создать**, чтобы создать среду выполнения интеграции Azure SSIS. 

   ![Доступ к среде выполнения интеграции через меню](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. В окне **Integration Runtime Setup** (Настройка среды выполнения интеграции) выберите **Lift-and-shift existing SSIS packages to execute in Azure** (Перенос имеющихся пакетов служб SSIS по методике lift-and-shift для выполнения в Azure), а затем щелкните **Далее**.

   ![Указание типа среды выполнения интеграции](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Остальные шаги описаны в разделе [Подготовка среды выполнения интеграции Azure SSIS](#provision-an-azure-ssis-integration-runtime). 

 
## <a name="deploy-ssis-packages"></a>Развертывание пакетов служб SSIS.
Теперь используйте SQL Server Data Tools (SSDT) или SQL Server Management Studio (SSMS) для развертывания пакетов служб SSIS в Azure. Подключитесь к серверу базы данных Azure, на котором размещен каталог служб SSIS (база данных SSISDB). Имя сервера базы данных Azure имеет формат `<servername>.database.windows.net` (для базы данных SQL Azure). 

См. документацию SSIS в следующих статьях: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (Развертывание, запуск и отслеживание пакета SSIS в Azure)   
- [Подключение к базе данных каталога SSISDB в Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule the execution of an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Планирование выполнения пакета SSIS в Azure)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Подключение к локальным источникам данных с помощью аутентификации Windows) 

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи: 

> [!div class="checklist"]
> * Создадите фабрику данных.
> * Подготовка среды выполнения интеграции Azure SSIS

Перейдите к следующему руководству, чтобы узнать о копировании данных из локальной среды в облако: 

> [!div class="nextstepaction"]
> [Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL Azure с помощью фабрики данных Azure](tutorial-copy-data-portal.md)
