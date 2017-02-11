---
title: "Создание расписания запуска и завершения работы виртуальной машины Azure с помощью тегов в формате JSON | Документация Майкрософт"
description: "В этой статье демонстрируется использование строк JSON в тегах для автоматизации планирования запуска и завершения работы виртуальной машины."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2016
ms.author: magoedte;paulomarquesc
translationtype: Human Translation
ms.sourcegitcommit: 00b217a4cddac0a893564db27ffb4f460973c246
ms.openlocfilehash: 4a9886cf5ee80bafd4b36d0d7f6781aea9b36dd6


---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Сценарий службы автоматизации Azure: создание расписания запуска и завершения работы виртуальной машины Azure с помощью тегов в формате JSON
Часто клиентам необходимо планировать запуск и завершение работы виртуальных машин, чтобы уменьшить затраты на подписку или соблюсти деловые и технические требования.  

Приведенный ниже сценарий позволяет настроить автоматические запуск и завершение работы виртуальных машин, используя тег Schedule на уровне группы ресурсов или виртуальной машины в Azure. Это расписание можно настроить с воскресенья по субботу с указанием времени запуска и времени завершения работы.  

Для реализации этой возможности используются некоторые готовые функции. К ним относятся:

* [наборы масштабирования виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) с параметрами автомасштабирования, которые позволяют увеличивать или уменьшать масштаб;
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) со встроенными возможностями планирования операций запуска и завершения работы.

Эти функции подходят только для определенных сценариев и не могут использоваться для виртуальных машин IaaS.   

Если тег Schedule применяется к группе ресурсов, то он также будет применен ко всем виртуальным машинам в ней. Если же непосредственно к виртуальной машине применяется другое расписание, то приоритетность расписаний устанавливается в следующем порядке:

1. расписание, примененное к группе ресурсов;
2. расписание, примененное к группе ресурсов и виртуальной машине в этой группе ресурсов;
3. расписание, примененное к виртуальной машине.

Этот сценарий фактически принимает строку JSON в указанном формате и добавляет ее в качестве значения для тега Schedule. Затем модуль Runbook получает список всех групп ресурсов и виртуальных машин и определяет расписание для каждой виртуальной машины на основе указанных выше сценариев. После этого он перебирает виртуальные машины по заданным расписаниям и оценивает действие, которое должно быть выполнено: остановка, завершение работы либо пропуск действия.

Эти модули Runbook выполняют аутентификацию с помощью [учетной записи запуска от имени Azure](automation-sec-configure-azure-runas-account.md).

## <a name="download-the-runbooks-for-the-scenario"></a>Скачивание модулей Runbook для сценария
Сценарий состоит из четырех модулей Runbook рабочего процесса PowerShell, которые можно скачать из [коллекции TechNet](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) или репозитория [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) для этого проекта.

| Модуль Runbook | Описание |
| --- | --- |
| Test-ResourceSchedule |Проверяет расписание каждой виртуальной машины и выполняет завершение работы или запуск в зависимости от расписания |
| Add-ResourceSchedule |Добавляет тег Schedule для виртуальной машины или группы ресурсов |
| Update-ResourceSchedule |Изменяет имеющийся тег Schedule, заменяя его новым |
| Remove-ResourceSchedule |Удаляет тег Schedule виртуальной машины или группы ресурсов |

## <a name="install-and-configure-this-scenario"></a>Установка и настройка сценария
### <a name="install-and-publish-the-runbooks"></a>Установка и публикация модулей Runbook
Скачав модули Runbook, импортируйте их, как описано в статье [Создание или импорт модуля Runbook в службе автоматизации Azure](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).  Опубликуйте каждый модуль Runbook после импорта в учетную запись автоматизации.

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>Добавление расписания в модуль Runbook Test-ResourceSchedule
Выполните следующие действия, чтобы включить расписание в модуль Runbook Test-ResourceSchedule. Это модуль Runbook, который проверяет, какую виртуальную машину следует запустить, отключить или оставить как есть.

1. На портале Azure откройте свою учетную запись службы автоматизации и щелкните элемент **Модули Runbook** .
2. В колонке **Test-ResourceSchedule** щелкните элемент **Расписания**.
3. В колонке **Расписания** щелкните **Добавить расписание**.
4. В колонке **Расписания** выберите **Связать расписание с модулем Runbook**, а затем — **Создать новое расписание**.
5. В колонке **Новое расписание** введите имя расписания, например *HourlyExecution*.
6. В поле **Запуск**расписания задайте время запуска, округленное до часов.
7. Выберите **Повторение** и для интервала **Повторять кажд.** выберите значение **1 час**.
8. Для параметра **Задать срок действия** задайте значение **Нет** и нажмите кнопку **Создать**, чтобы сохранить новое расписание.
9. В колонке параметров **Включить модуль Runbook в расписание** выберите **Параметры и настройки запуска**. В колонке **Параметры** модуля Test-ResourceSchedule в поле **SubscriptionName** введите имя подписки.  Это единственный параметр, необходимый для модуля Runbook.  Закончив, нажмите кнопку **ОК**.  

После этого расписание Runbook должно выглядеть следующим образом.

![Настроенный модуль Runbook Test-ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>Форматирование строки JSON
Это решение фактически принимает строку JSON в указанном формате и добавляет ее в качестве значения для тега Schedule. Затем модуль Runbook получает список всех групп ресурсов и виртуальных машин и определяет расписания для каждой виртуальной машины.

Он перебирает эти виртуальные машины по заданным расписаниям и проверяет, какое действие следует выполнить. Ниже приведен пример соответствующего форматирования.

    {
       "TzId": "Eastern Standard Time",
        "0": {  
           "S": "11",
           "E": "17"
        },
        "1": {
           "S": "9",
           "E": "19"
        },
        "2": {
           "S": "9",
           "E": "19"
        },
    }

Вот подробные сведения об этой структуре:

1. Формат этой структуры JSON оптимизирован для обхода ограничения в 256 знаков на значение тега в Azure.
2. *TzId* представляет часовой пояс виртуальной машины. Этот идентификатор можно получить с помощью класса TimeZoneInfo .NET в сеансе PowerShell:**[System.TimeZoneInfo]::GetSystemTimeZones()**.

    ![GetSystemTimeZones в PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * Дни недели представлены числом от нуля до шести. Значение 0 соответствует воскресенью.
   * Время запуска представлено атрибутом **S** и его значением в 24-часовом формате.
   * Время окончания или завершения работы представлено атрибутом **E** и его значением в 24-часовом формате.

     Если атрибуты **S** и **E** имеют значение 0 (ноль), то во время проверки виртуальная машина останется в своем текущем состоянии.   
3. Если вы хотите пропустить проверку в определенный день недели, то не добавляйте раздел этого дня недели. В следующем примере проверка выполняется только в понедельник, а в остальные дни недели она пропускается.

        {
          "TzId": "Eastern Standard Time",
           "1": {
             "S": "11",
             "E": "17"
           }
        }

## <a name="tag-resource-groups-or-vms"></a>Добавление тегов для групп ресурсов или виртуальных машин
Для завершения работы виртуальных машин необходимо отметить тегом их или группы ресурсов, в которых они находятся. Виртуальные машины, у которых нет тега Schedule, не проверяются. По этой причине их запуск или завершение их работы не выполняется.

Существует два способа маркировки виртуальных машин или групп ресурсов при использовании этого решения. Это можно сделать непосредственно на портале или с помощью модулей Runbook Add-ResourceSchedule, Update-ResourceSchedule и Remove-ResourceSchedule.

### <a name="tag-through-the-portal"></a>Добавление тегов с помощью портала
Выполните следующие действия, чтобы добавить тег для виртуальной машины или группы ресурсов на портале.

1. Преобразуйте строку JSON в плоскую структуру и убедитесь, что в ней нет пробелов.  Строка JSON должна выглядеть следующим образом.

        {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
2. Щелкните значок **Тег** для виртуальной машины или группы ресурсов, чтобы применить это расписание.

![Параметр тега виртуальной машины](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)    

1. Теги определяются следующей парой "ключ — значение". Введите **Schedule** в поле **Ключ** и вставьте строку JSON в поле **Значение**. Щелкните **Сохранить**. Новый тег должен появиться в списке тегов для ресурса.

![Тег Schedule виртуальной машины](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>Добавление тега из PowerShell
Все импортированные модули Runbook содержат справочные сведения в начале скрипта, в которых описывается выполнение Runbook непосредственно из PowerShell. Модули Runbook Add-ScheduleResource и Update-ScheduleResource можно вызывать из PowerShell. Для этого нужно передать необходимые параметры, позволяющие создать или обновить тег Schedule для виртуальной машины или группы ресурсов за пределами портала.  

Чтобы создавать, добавлять и удалять теги с помощью PowerShell, необходимо сначала настроить [среду PowerShell для Azure](../powershell-install-configure.md). После завершения настройки можно перейти к следующим действиям.

### <a name="create-a-schedule-tag-with-powershell"></a>Создание тега Schedule с помощью PowerShell
1. Откройте сеанс PowerShell. Затем выполните следующую команду, чтобы пройти аутентификацию с помощью учетной записи запуска от имени и указать подписку.   

        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"
2. Определите хэш-таблицу расписания. Ниже приведен пример ее структуры.

        $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
3. Определите параметры, необходимые для Runbook. В следующем примере в качестве цели используется виртуальная машина.

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "VmName"="VM01";"Schedule"=$schedule}

    Если вы отмечаете тегами группу ресурсов, удалите параметр *VMName* из хэш-таблицы $params следующим образом.

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "Schedule"=$schedule}
4. Выполните модуль Runbook Add-ResourceSchedule со следующими параметрами, чтобы создать тег Schedule.

        Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
5. Чтобы обновить тег группы ресурсов или виртуальной машины, выполните модуль Runbook **Update-ResourceSchedule** со следующими параметрами.

        Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

### <a name="remove-a-schedule-tag-with-powershell"></a>Удаление тега Schedule с помощью PowerShell
1. Откройте сеанс PowerShell и выполните следующую команду, чтобы пройти аутентификацию с помощью учетной записи запуска от имени, а также выбрать и указать подписку.

        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"
2. Определите параметры, необходимые для Runbook. В следующем примере в качестве цели используется виртуальная машина.

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01" `
        ;"VmName"="VM01"}

    Если вы удаляете тег группы ресурсов, удалите параметр *VMName* из хэш-таблицы $params следующим образом.

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
3. Выполните модуль Runbook Remove-ResourceSchedule, чтобы удалить тег Schedule.

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
4. Чтобы обновить тег группы ресурсов или виртуальной машины, выполните модуль Runbook Remove-ResourceSchedule со следующими параметрами.

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

> [!NOTE]
> Рекомендуется применять упреждающий мониторинг этих модулей Runbook (и состояния виртуальной машины), чтобы проверять, соответствующим ли образом выполняется запуск и завершение работы виртуальных машин.  
>
>

Сведения о задании Runbook Test-ResourceSchedule можно просмотреть на портале Azure, выбрав элемент **Задания** этого модуля Runbook. Помимо общей информации о задании, сводка задания будет содержать входные параметры и поток вывода, а также все возникшие исключения.  

**Сводка по заданию** содержит сообщения из потоков вывода, предупреждений и ошибок. Выберите элемент **Вывод** , чтобы просмотреть подробные сведения о результатах выполнения модуля Runbook.

![Выходные данные Test-ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)  

## <a name="next-steps"></a>Дальнейшие действия
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Первый Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md).
* Дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях см. в статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md).
* Дополнительные сведения о функции поддержки сценариев PowerShell см. в публикации блога [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Поддержка собственных сценариев PowerShell в службе автоматизации Azure).
* Чтобы узнать больше о ведении журнала и выходных данных Runbook, ознакомьтесь со статьей [Выходные данные и сообщения Runbook в службе автоматизации Azure](automation-runbook-output-and-messages.md).
* Дополнительные сведения об учетной записи запуска от имени Azure и о том, как с ее помощью выполнить аутентификацию модулей Runbook, см. в статье [Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени](automation-sec-configure-azure-runas-account.md).



<!--HONumber=Nov16_HO3-->


