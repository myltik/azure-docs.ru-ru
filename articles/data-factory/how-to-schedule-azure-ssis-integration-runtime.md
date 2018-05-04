---
title: Как планировать среду выполнения интеграции Azure SSIS | Документация Майкрософт
description: В этой статье описан процесс планирования запуска и остановки среды выполнения интеграции Azure SSIS с использованием службы автоматизации Azure и фабрики данных.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 04/17/2018
ms.author: douglasl
ms.openlocfilehash: 3e69c147201ab7f3c5e2cf61e72bdb8073354e67
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-schedule-starting-and-stopping-of-an-azure-ssis-integration-runtime"></a>Как планировать запуск и остановку среды выполнения интеграции Azure SSIS 
Запуск среды выполнения интеграции (IR) Azure SSIS (SQL Server Integration Services) связан с издержками. Поэтому следует запускать IR только в том случае, когда требуется выполнение пакетов SSIS в Azure, и останавливать ее при ненадобности. Вы можете [запустить или остановить IR Azure SSIS вручную](manage-azure-ssis-integration-runtime.md) с помощью пользовательского интерфейса фабрики данных или Azure PowerShell. В этой статье описан процесс планирования запуска и остановки IR Azure SSIS с помощью службы автоматизации Azure и фабрики данных Azure. Ниже приведены общие шаги, описанные в этой статье:

1. **Создание и тестирование модуля runbook службы автоматизации Azure.** На этом шаге с помощью скрипта, который запускает или останавливает IR Azure SSIS, создается модуль runbook PowerShell. Затем необходимо протестировать runbook в сценариях START и STOP и убедиться в том, что IR запускается или останавливается. 
2. **Создание двух расписаний для runbook.** Для первого расписания вы настраиваете runbook, используя START в качестве значения для операции. Для второго расписания вы настраиваете runbook, используя STOP в качестве значения для операции. Для обоих расписаний укажите периодичность, с которой выполняется runbook. Например, можно запланировать запуск первого расписания на 8:00 ежедневно, а второго — 23:00 ежедневно. При выполнении первого runbook среда IR Azure SSIS запускается. При выполнении второго runbook среда IR Azure SSIS останавливается. 
3. **Создание двух веб-перехватчиков для runbook** — один для операции START, а другой для операции STOP. Используйте URL-адреса этих веб-перехватчиков при настройке веб-действий в конвейере фабрики данных. 
4. **Создание конвейера фабрики данных.** Конвейер, который вы создаете, состоит из трех действий. Первое **веб-** действие заставляет первый веб-перехватчик запустить IR Azure SSIS. Действие **хранимой процедуры** выполняет скрипт SQL, который запускает пакет SSIS. Второе **веб-** действие останавливает IR Azure SSIS. Дополнительные сведения о вызове пакета SSIS из конвейера фабрики данных с помощью действия хранимой процедуры см. в [этой статье](how-to-invoke-ssis-package-stored-procedure-activity.md). Затем вы создадите триггер расписания, чтобы запланировать запуск конвейера с указанной периодичностью.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете версию 1 службы "Фабрика данных", которая является общедоступной, ознакомьтесь [с этой статьей](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

 
## <a name="prerequisites"></a>предварительным требованиям
Если среда выполнения интеграции Azure SSIS еще не подготовлена, подготовьте ее, следуя указаниям из этого [руководства](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Создание и тестирование модуля runbook службы автоматизации Azure
В этом разделе описывается выполнение следующих шагов: 

1. Создание учетной записи службы автоматизации Azure.
2. Создание runbook PowerShell в учетной записи службы автоматизации Azure. Сценарий PowerShell, связанный с runbook, запускает или останавливает IR Azure SSIS в зависимости от команды, указанной для параметра OPERATION. 
3. Протестируйте runbook в сценариях запуска и остановки, чтобы убедиться в том, что он работает. 

### <a name="create-an-azure-automation-account"></a>Создание учетной записи службы автоматизации Azure
Если у вас нет учетной записи службы автоматизации Azure, создайте ее, следуя инструкциям в этом шаге. Подробные инструкции см. в статье [Создание учетной записи службы автоматизации Azure](../automation/automation-quickstart-create-account.md). Кроме того, на этом шаге вы создаете учетную запись **запуска от имени Azure** (субъект-службу в Azure Active Directory) и добавляете ее в роль **участника** подписки Azure. Это должна быть подписка, которая содержит фабрику данных с IR Azure SSIS. В службе автоматизации Azure эта учетная запись используется для проверки подлинности Azure Resource Manager и работы с вашими ресурсами. 

1. Запустите веб-браузер **Microsoft Edge** или **Google Chrome**. Сейчас только эти браузеры поддерживают пользовательский интерфейс фабрики данных.
2. Войдите на [портал Azure](https://portal.azure.com/).    
3. В меню слева выберите **Создать**, **Мониторинг и управление** и **Служба автоматизации**. 

    ![Создать -> Мониторинг и управление -> Служба автоматизации](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. В окне **Добавление учетной записи службы автоматизации** сделайте следующее: 

    1. Укажите **имя** учетной записи службы автоматизации. 
    2. Выберите **подписку** с фабрикой данных с IR Azure SSIS. 
    3. Для поля **Группа ресурсов** выберите **Создать новый**, чтобы создать группу ресурсов, или **Использовать имеющийся**, чтобы выбрать имеющуюся группу ресурсов. 
    4. Выберите **расположение** для учетной записи службы автоматизации. 
    5. Убедитесь, что для параметра **Создать учетную запись запуска от имени** выбрано вариант **Да**. Субъект-служба будет создана в Azure Active Directory. Она добавлена в роль **участника** подписки Azure.
    6. Установите флажок **Закрепить на панели мониторинга**, чтобы она отображалась на панели мониторинга портала. 
    7. Нажмите кнопку **Создать**. 

        ![Создать -> Мониторинг и управление -> Служба автоматизации](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. **Состояние развертывания** отображается на панели мониторинга и в уведомлениях. 
    
    ![Развертывание службы автоматизации](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. После успешного создания учетной записи службы автоматизации появится ее домашняя страница. 

    ![Домашняя страница службы автоматизации](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Импорт модулей фабрики данных

1. Выберите **Модули** в разделе **Общие ресурсы** в меню слева и проверьте, есть ли в списке модулей **AzureRM.Profile** и **AzureRM.DataFactoryV2**. Если их там нет, выберите **Обзор коллекции** на панели инструментов.

    ![Домашняя страница службы автоматизации](./media/how-to-schedule-azure-ssis-integration-runtime/automation-modules.png)
2. В окне **Обзор коллекции** введите **AzureRM.Profile** в окне поиска и нажмите клавишу **ВВОД**. Выберите **AzureRM.Profile** в списке. Затем нажмите кнопку **Импорт** на панели инструментов. 

    ![Выбор AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/select-azurerm-profile.png)
1. В окне **Импорт** выберите параметр **Я соглашаюсь обновить все модули Azure** и нажмите кнопку **ОК**.  

    ![Импорт AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/import-azurerm-profile.png)
4. Закройте окно, чтобы вернуться к окну **Модули**. Состояние импорта должно отображаться в списке. Щелкните **Обновить**, чтобы обновить список. Подождите, пока значение поля **Состояние** не изменится на **Доступно**.

    ![Состояние импорта](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-profile.png)
1. Повторите шаги, чтобы импортировать модуль **AzureRM.DataFactoryV2**. Прежде чем продолжить, убедитесь, что для состояния этого модуля установлено значение **Доступно**. 

    ![Окончательное состояние импорта](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-datafactoryv2.png)

### <a name="create-a-powershell-runbook"></a>Создание модуля runbook в PowerShell
В следующей процедуре приведены шаги для создания runbook PowerShell. Сценарий, связанный с runbook, запускает или останавливает IR Azure SSIS в зависимости от команды, указанной для параметра **OPERATION**. В этом разделе предоставлены не все сведения по созданию runbook. Дополнительные сведения см. в статье [Создание runbook службы автоматизации Azure](../automation/automation-quickstart-create-runbook.md).

1. Перейдите на вкладку **Модули Runbook** и выберите **+ Добавить Runbook** на панели инструментов. 

    ![Кнопка добавления модуля runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Выберите **Создать новый Runbook** и сделайте следующее: 

    1. В поле **Имя** введите **StartStopAzureSsisRuntime**.
    2. Для поля **Тип Runbook** выберите значение **PowerShell**.
    3. Нажмите кнопку **Создать**.
    
        ![Кнопка добавления модуля runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Скопируйте и вставьте следующий сценарий в окно сценария runbook. Сохраните и опубликуйте runbook, нажав кнопки **Сохранить** и **Опубликовать** на панели инструментов. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![Редактирование модуля runbook PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. Протестируйте модуль runbook, нажав кнопку **Запуск** на панели инструментов. 

    ![Кнопка запуска модуля runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. В окне **Запуск Runbook** сделайте следующее: 

    1. Для параметра **RESOURCEGROUPNAME** введите имя группы ресурсов с фабрикой данных с IR Azure SSIS. 
    2. Для параметра **DATAFACTORYNAME** введите имя фабрики данных с IR Azure SSIS. 
    3. Для параметра **AZURESSISNAME** введите имя IR Azure SSIS. 
    4. Для параметра **OPERATION** введите **START**. 
    5. Нажмите кнопку **ОК**.  

        ![Окно "Запуск Runbook"](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. В окне "Задание" выберите плитку **Вывод**. В окне вывода задания дождитесь появления сообщения **### Завершено ###** после появления **### Запуск ###**. Запуск IR Azure SSIS занимает приблизительно 20 минут. Закройте окно **Задание** и вернитесь в окно **Runbook**.

    ![IR Azure SSIS запущена](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Повторите предыдущие два шага, но в качестве значения для параметра **OPERATION** используйте **STOP**. Запустите модуль runbook снова, нажав кнопку **Запуск** на панели инструментов. Укажите имена группы ресурсов, фабрики данных и среды выполнения интеграции Azure SSIS. Для параметра **OPERATION** введите **STOP**. 

    В окне вывода задания дождитесь появления сообщения **### Завершено ###** после появления **### Остановка ###**. Остановка IR Azure SSIS занимает меньше времени, чем ее запуск. Закройте окно **Задание** и вернитесь в окно **Runbook**.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Создание расписаний для запуска и остановки IR Azure SSIS с помощью runbook
В предыдущем разделе вы создали runbook службы автоматизации Azure, который может запустить или остановить IR Azure SSIS. В этом разделе вы создадите два расписания для runbook. При настройке первого расписания для параметра OPERATION необходимо задать значение START. Аналогичным образом при настройке второго расписания для параметра OPERATION необходимо указать STOP. Подробные инструкции по созданию расписаний см. в [этом разделе](../automation/automation-schedules.md#creating-a-schedule).

1. В окне **Runbook** выберите **Расписания** и щелкните **+ Добавить расписание** на панели инструментов. 

    ![IR Azure SSIS запущена](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. В окне **Включить модуль Runbook в расписание** сделайте следующее: 

    1. Выберите **Связать расписание с модулем Runbook**. 
    2. Выберите **Создать новое расписание**.
    3. В окне **Новое расписание** в поле **Имя** введите **Запускать IR ежедневно**. 
    4. В разделе **Начинается** укажите время на несколько минут позже текущего времени. 
    5. Для параметра **Периодичность** выберите значение **Повторяющееся**. 
    6. В разделе **Повторять каждую** выберите значение **День**. 
    7. Нажмите кнопку **Создать**. 

        ![Расписание для запуска IR Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Перейдите на вкладку **Параметры и настройки запуска**. Укажите имена группы ресурсов, фабрики данных и среды выполнения интеграции Azure SSIS. Для параметра **OPERATION** введите **START**. Нажмите кнопку **ОК**. Нажмите кнопку **ОК** еще раз, чтобы просмотреть расписание на странице **Расписания** модуля runbook. 

    ![Расписание для запуска IR Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Повторите предыдущие два шага, чтобы создать расписание с именем **Останавливать IR ежедневно**. На этот раз укажите время как минимум на 30 минут позже времени, указанного для расписания **Запускать IR ежедневно**. Для параметра **OPERATION** задайте значение **STOP**. 
5. В окне **Runbook** выберите **Задания** в меню слева. Вы увидите задания, созданные расписаниями в указанное время, и их состояния. Отобразятся сведения о задании, например его выходные данные, похожие на те, которые вы видели при тестировании модуля runbook. 

    ![Расписание для запуска IR Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. После завершения тестирования отключите расписания, отредактировав их и выбрав **Нет** для параметра **Включено**. Выберите **Расписания** в меню слева, выберите **Запускать IR ежедневно/Останавливать IR ежедневно** и выберите **Нет** для параметра **Включено**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Создание веб-перехватчиков для запуска и остановки IR Azure SSIS
Следуйте инструкциям в [этом разделе](../automation/automation-webhooks.md#creating-a-webhook), чтобы создать два веб-перехватчика для модуля runbook. Для первого веб-перехватчика задайте значение START для параметра OPERATION, а для второго — значение STOP. Сохраните URL-адреса для обоих веб-перехватчиков где-нибудь (например, в текстовом файле или блокноте OneNote). Используйте эти URL-адреса при настройке веб-действий в конвейере фабрики данных. На рисунке ниже показан пример создания веб-перехватчика, который запускает IR Azure SSIS:

1. В окне **Runbook** выберите **Веб-перехватчики** в меню слева и выберите **+ Добавить веб-перехватчик** на панели инструментов. 

    ![Веб-перехватчики -> Добавить веб-перехватчик](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. В окне **Добавить веб-перехватчик** выберите **Создать новый веб-перехватчик** и сделайте следующее: 

    1. В поле **Имя** введите **StartAzureSsisIR**. 
    2. Для параметра **Включено** выберите **Да**. 
    3. Скопируйте **URL-адрес** и сохраните его где-нибудь. Это важно. Вы больше не увидите URL-адрес. 
    4. Нажмите кнопку **ОК**. 

        ![Окно "Новый веб-перехватчик"](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Перейдите на вкладку **Параметры и настройки запуска**. Укажите имена группы ресурсов, фабрики данных и среды выполнения интеграции Azure SSIS. Для параметра **OPERATION** введите **START**. Последовательно выберите **ОК**. Затем щелкните **Создать**. 

    ![Веб-перехватчик — параметры и настройки запуска](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Повторите предыдущие три шага, чтобы создать другой веб-перехватчик с именем **StopAzureSsisIR**. Не забудьте скопировать URL-адрес. При указании параметров и настроек запуска введите **STOP** для параметра **OPERATION**. 

У вас должно быть два URL-адреса, один для веб-перехватчика **StartAzureSsisIR**, а другой для веб-перехватчика **StopAzureSsisIR**. На эти URL-адреса можно отправить запрос HTTP POST для запуска и остановки IR Azure SSIS. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Создание и планирование конвейера фабрики данных, который запускает или останавливает IR
В этом разделе показано, как использовать веб-действие для вызова веб-перехватчиков, созданных в предыдущем разделе.

Конвейер, который вы создаете, состоит из трех действий. 

1. Первое **веб-** действие заставляет первый веб-перехватчик запустить IR Azure SSIS. 
2. Действие **Выполнить пакет SSIS** и действие **Хранимая процедура** запускают пакет SSIS.
3. Второе **веб-** действие заставляет веб-перехватчик остановить IR Azure SSIS. 

После создания и тестирования конвейера необходимо создать триггер расписания и связать его с конвейером. Триггер расписания определяет расписание для конвейера. Предположим, что вы создали триггер, запуск которого запланирован ежедневно в 23:00. Триггер выполняет конвейер в 23:00 ежедневно. Конвейер запускает IR Azure SSIS, выполняет пакет SSIS и останавливает IR Azure SSIS. 

### <a name="create-a-data-factory"></a>Создание фабрики данных

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

### <a name="create-a-pipeline"></a>Создание конвейера

1. На странице **Начало работы** щелкните **Create pipeline** (Создать конвейер). 

   ![Страница "Начало работы"](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. На панели элементов **Действия** разверните **Общие** и перетащите **веб-** действие в область конструктора конвейера. На вкладке **Общие** в окне **Свойства** измените имя действия на **StartIR**.

   ![Первое веб-действие — вкладка "Общие"](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Перейдите на вкладку **Настройки** в окне **Свойства** и сделайте следующее: 

    1. В поле **URL-адрес** вставьте URL-адрес для веб-перехватчика, который запускает IR Azure SSIS. 
    2. Для поля **Метод** выберите **POST**. 
    3. В поле **Текст** введите `{"message":"hello world"}`. 
   
        ![Первое веб-действие — вкладка "Настройки"](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. Перетащите действие "Выполнить пакет SSIS" или действие "Хранимая процедура" из раздела **Общие** панели элементов **Действия**. Для имени действия задайте значение **RunSSISPackage**. 

5. Если выбрано действие "Выполнить пакет SSIS", следуйте инструкциям в разделе [Запуск пакета SSIS с помощью действия SSIS в фабрике данных Azure](how-to-invoke-ssis-package-ssis-activity.md), чтобы завершить создание действия.  Обязательно укажите достаточное количество повторных попыток, которые предпринимаются достаточно часто для ожидания доступности среды интеграции Azure-SSIS, так как ее запуск занимает до 30 минут. 

    ![Параметры повторных попыток](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. Если выбрано действие хранимой процедуры, следуйте инструкциям в разделе [Вызов пакета SSIS с помощью действия хранимой процедуры в фабрике данных Azure](how-to-invoke-ssis-package-stored-procedure-activity.md), чтобы завершить создание действия. Обязательно вставьте сценарий Transact-SQL, который ожидает доступности среды интеграции Azure-SSIS, так как ее запуск занимает до 30 минут.
    ```sql
    DECLARE @return_value int, @exe_id bigint, @err_msg nvarchar(150)

    -- Wait until Azure-SSIS IR is started
    WHILE NOT EXISTS (SELECT * FROM [SSISDB].[catalog].[worker_agents] WHERE IsEnabled = 1 AND LastOnlineTime > DATEADD(MINUTE, -10, SYSDATETIMEOFFSET()))
    BEGIN
        WAITFOR DELAY '00:00:01';
    END

    EXEC @return_value = [SSISDB].[catalog].[create_execution] @folder_name=N'YourFolder',
        @project_name=N'YourProject', @package_name=N'YourPackage',
        @use32bitruntime=0, @runincluster=1, @useanyworker=1,
        @execution_id=@exe_id OUTPUT 

    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1

    EXEC [SSISDB].[catalog].[start_execution] @execution_id = @exe_id, @retry_count = 0

    -- Raise an error for unsuccessful package execution, check package execution status = created (1)/running (2)/canceled (3)/
    -- failed (4)/pending (5)/ended unexpectedly (6)/succeeded (7)/stopping (8)/completed (9) 
    IF (SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id = @exe_id) <> 7 
    BEGIN
        SET @err_msg=N'Your package execution did not succeed for execution ID: '+ CAST(@execution_id as nvarchar(20))
        RAISERROR(@err_msg, 15, 1)
    END
    ```

7. Привяжите действие **Веб-действие** к действию **Выполнить пакет SSIS** или **Хранимая процедура**. 

    ![Связывание действий "Веб-действие" и "Хранимая процедура"](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. Перетащите другое действие **Веб-действие** в область справа от действия **Выполнить пакет SSIS** или **Хранимая процедура**. Для имени действия задайте значение **StopIR**. 
9. Перейдите на вкладку **Настройки** в окне **Свойства** и сделайте следующее: 

    1. В поле **URL-адрес** вставьте URL-адрес для веб-перехватчика, который останавливает IR Azure SSIS. 
    2. Для поля **Метод** выберите **POST**. 
    3. В поле **Текст** введите `{"message":"hello world"}`.  
10. Привяжите действие **Выполнить пакет SSIS** или действие **Хранимая процедура** к последнему действию **Веб-действие**.

    ![Полный конвейер](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. Проверьте настройки конвейера, нажав кнопку **Проверить** на панели инструментов. Закройте **отчет о проверке конвейера**, нажав кнопку **>>**. 

    ![Проверка конвейера](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Тестовое выполнение конвейера

1. На панели инструментов для конвейера щелкните **Тестовый запуск**. В окне **Выходные данные** в нижней области отобразятся выходные данные. 

    ![Тестовый запуск](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. На странице **Runbook** учетной записи службы автоматизации Azure можно проверить, что задания выполняются для запуска и остановки IR Azure SSIS. 

    ![Задания runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Запустите SQL Server Management Studio. В окне **Подключение к серверу** сделайте следующее: 

    1. В поле **Имя сервера** укажите **&lt;ваша база данных Azure SQL&gt;.database.windows.net**.
    2. Выберите **Параметры >>**.
    3. В поле **Подключение к базе данных** выберите **SSISDB**.
    4. Нажмите кнопку **Подключиться**. 
    5. Разверните **Каталоги служб Integration Services** -> **SSISDB** -> Ваша папка -> **Проекты** -> Ваш проект SSIS -> **Пакеты**. 
    6. Щелкните правой кнопкой мыши пакет SSIS и выберите **Отчеты** -> **Стандартные отчеты** -> **Все выполнения**. 
    7. Убедитесь, что пакет SSIS запущен. 

        ![Проверка запуска пакета SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Планирование запуска конвейера 
Теперь, когда конвейер работает правильно, можно создать триггер для запуска этого конвейера с указанной периодичностью. Дополнительные сведения о связывании триггера расписания с конвейером см. в разделе [Запуск конвейера по расписанию](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. На панели инструментов для конвейера выберите **Триггер** и **Создать/изменить**. 

    !["Триггер" > "Создать/изменить"](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. В окне **Add Triggers** (Добавление триггеров) выберите **+ Создать**.

    !["Добавление триггеров" — "Создать"](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. На странице **Новый триггер** сделайте следующее: 

    1. В поле **Имя** укажите имя триггера. В следующем примере имя триггера — **Запускать ежедневно**. 
    2. В поле **Тип** выберите **Расписание**. 
    3. В поле **Дата начала** выберите дату и время начала. 
    4. В поле **Повторение** укажите периодичность для триггера. В следующем примере указано раз в день. 
    5. В поле **Окончание** можно указать дату и время, выбрав параметр **Дата**. 
    6. Выберите **Активировано**. Триггер будет активирован сразу после публикации решения в фабрике данных. 
    7. Щелкните **Далее**.

        !["Триггер" > "Создать/изменить"](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. Прочитайте предупреждение на странице **Trigger Run Parameters** (Параметры выполнения триггера) и выберите **Готово**. 
5. Опубликуйте решение в фабрике данных, выбрав **Опубликовать все** в области слева. 

    ![Опубликовать все](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)
6. Отслеживайте запуски триггера и конвейера на вкладке **Мониторинг** в левой части экрана. Подробные инструкции см. в разделе [Мониторинг конвейера](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Запуски конвейера](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
7. Чтобы просмотреть выполнения действий, связанные с запуском конвейера, щелкните первую ссылку (**View Activity Runs** (Просмотр запусков действий)) в столбце **Действия**. Вы увидите три выполнения действий, связанные с каждым действием в конвейере (первое веб-действие, действие хранимой процедуры и второе веб-действие). Чтобы вернуться к просмотру запусков конвейера, выберите ссылку **Конвейеры** в верхней части окна.

    ![Выполнение действия](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
8. Запуски триггера можно также просмотреть, выбрав пункт списка **Trigger runs** (Запуски триггера) из раскрывающегося списка рядом с пунктом **Pipeline Runs** (Запуски конвейера) в верхней части экрана. 

    ![Выполнения триггеров](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

## <a name="next-steps"></a>Дополнительная информация
См. документацию SSIS в следующих статьях: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (Развертывание, запуск и отслеживание пакета SSIS в Azure)   
- [Connect to the SSISDB Catalog database on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Подключение к базе данных каталога SSISDB в Azure)
- [Schedule the execution of an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Планирование выполнения пакета SSIS в Azure)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Подключение к локальным источникам данных с помощью аутентификации Windows)
