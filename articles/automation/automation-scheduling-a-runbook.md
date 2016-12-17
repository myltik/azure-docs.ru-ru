---
title: "Планирование запуска модуля Runbook в службе автоматизации Azure | Документация Майкрософт"
description: "Рассматривается создание расписания в службе автоматизации Azure, которое позволяет запускать модуль Runbook однократно или несколько раз в определенное время."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 710979ff-99d8-41e4-ac6d-6bf26b8ea654
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: bwren
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ed7bd79face77a132a4a6588e72b7511bab4b4d4

---

# <a name="scheduling-a-runbook-in-azure-automation"></a>Создание расписания для Runbook в службе автоматизации Azure
Чтобы запускать модуль Runbook в определенное время с помощью расписания в службе автоматизации Azure, его необходимо привязать к одному или нескольким расписаниям. На классическом портале Azure вы можете настроить расписание для однократного, ежечасного или ежедневного выполнения модулей Runbook. На портале Azure, кроме того, можно запланировать выполнение еженедельно, ежемесячно, в конкретные дни недели или месяца, а также в определенный день месяца.  Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook.

## <a name="creating-a-schedule"></a>Создание расписания
Новое расписание для модулей Runbook вы можете создать на портале Azure, на классическом портале Azure или с помощью Windows PowerShell. Также расписание можно создать при связывании Runbook с расписанием с помощью портала Azure или классического портала Azure.

> [!NOTE]
> При связывании расписания с модулем Runbook служба автоматизации сохраняет текущие версии модулей в вашей учетной записи и связывает их с данным расписанием.  Это означает, что если при создании расписания в вашей учетной записи был модуль версии 1.0, и затем этот модуль был обновлен до версии 2.0, то расписание по-прежнему будет использовать версию 1.0.  Чтобы использовать обновленную версию модуля, необходимо создать новое расписание. 
> 
> 

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Создание нового расписания на классическом портале Azure
1. На классическом портале Azure щелкните "Служба автоматизации", а затем выберите имя учетной записи службы автоматизации.
2. Выберите вкладку **Средства** .
3. Нажмите кнопку **Добавить параметр**в нижней части окна.
4. Щелкните **Добавить расписание**.
5. Введите **имя** и при желании **описание** нового расписания. Расписание может выполняться **однократно**, **ежечасно**, **ежедневно**, **еженедельно** или **ежемесячно**.
6. Укажите **Время начала** и другие параметры в зависимости от типа выбранного расписания.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Создание нового расписания на портале Azure
1. На портале Azure из учетной записи службы автоматизации щелкните элемент **Ресурсы**, чтобы открыть колонку **Ресурсы**.
2. Щелкните элемент **Расписания**, чтобы открыть колонку **Расписания**.
3. Щелкните **Добавить расписание** в верхней части этой колонки.
4. В колонке **Новое расписание** введите **имя** и при желании **описание** нового расписания.
5. Выберите режим выполнения расписания: **Однократно** или **Повторение**.  Если вы выбрали режим **Однократно**, то укажите **время начала** и нажмите кнопку **Создать**.  Если вы выбрали режим **Повторение**, то укажите **время начала** и частоту повторений для модуля Runbook: каждый **час**, **день**, **неделю** или **месяц**.  Если в этом раскрывающемся списке вы выбрали **неделю** или **месяц**, то в колонке появится элемент **Recurrence option** (Параметры периодичности). Щелкнув его, вы откроете колонку **Recurrence option** (Параметры периодичности). Здесь вы сможете выбрать день недели, если ранее выбрали параметр **неделя**.  Если же вы выбрали параметр **месяц**, то здесь сможете выбрать **дни недели** или конкретные дни месяца из календаря, а также указать, будет ли модуль выполняться в последний день месяца. Завершив настройку, нажмите кнопку **OK**.   

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Создание расписания с помощью Windows PowerShell
В службе автоматизации Azure вы можете использовать командлет [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx), чтобы создать новое расписание для классических модулей Runbook, или командлет [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx), чтобы создать расписание для модулей Runbook на портале Azure. Укажите для расписания время начала и частоту выполнения.

Приведенные ниже примеры команд демонстрируют, как с помощью командлета управления службами Azure создать новое расписание, которое будет запускаться каждый день в 15:30 начиная с 20 января 2015 г.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

Приведенные ниже примеры команд демонстрируют, как с помощью командлета Azure Resource Manager создать расписание для запуска в 15-й и 30-й день каждого месяца.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"


## <a name="linking-a-schedule-to-a-runbook"></a>Связывание расписания с модулем Runbook
Один модуль Runbook может быть связан с несколькими расписаниями, и одно расписание может иметь несколько привязанных модулей Runbook. Если Runbook имеет параметры, для них можно указать значения. Необходимо предоставить значения для всех обязательных параметров, а также значения для необязательных параметров.  Значения будут применяться каждый раз при запуске модуля Runbook с помощью расписания.  Один и тот же модуль Runbook можно привязать к другому расписанию и указать другие значения параметров.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Связывание расписания с модулем Runbook с помощью классического портала Azure
1. На классическом портале Azure щелкните **Служба автоматизации** , а затем — имя учетной записи службы автоматизации.
2. Выберите вкладку **Модули Runbook** .
3. Щелкните имя одного модуля Runbook для привязки к расписанию.
4. Перейдите на вкладку **Расписание** .
5. Если модуль Runbook в настоящее время не связан с расписанием, будет предоставлена возможность выбрать **ссылку на новое расписание** или **ссылку на существующее расписание**.  Если модуль в настоящее время связан с расписанием, щелкните **Ссылки** в нижней части окна для доступа к этим параметрам.
6. Если модуль Runbook имеет параметры, их необходимо будет заполнить.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Связывание расписания с модулем Runbook с помощью портала Azure
1. На портале Azure из учетной записи службы автоматизации щелкните элемент **Модули Runbook**, чтобы открыть колонку **Модули Runbook**.
2. Щелкните имя одного модуля Runbook для привязки к расписанию.
3. Если модуль Runbook сейчас не связан с расписанием, вы сможете создать новое расписание или связать модуль с существующим расписанием.  
4. Если модуль Runbook имеет параметры, следует выбрать действие **Изменить параметры запуска (по умолчанию: Azure)**, чтобы открыть колонку **Параметры**, где вы сможете ввести нужные данные.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Связывание расписания с модулем Runbook с помощью Windows PowerShell
Вы можете использовать командлет [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx), чтобы связать расписание с классическим модулем Runbook, или командлет [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) для модулей Runbook на портале Azure.  С помощью параметра "Параметры" можно указать значения параметров для модуля Runbook. Дополнительные сведения об указании значений параметров см. в разделе [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

Приведенные ниже примеры команд демонстрируют, как связать расписание, используя командлет управления службами Azure с параметрами.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

Приведенные ниже примеры команд демонстрируют, как связать расписание с модулем Runbook, используя командлет управления службами Azure с параметрами.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"

## <a name="disabling-a-schedule"></a>Отключение расписания
Если расписание отключено, модули Runbook, связанные с ним, больше не будут запускаться по такому расписанию. Можно отключить расписание вручную или указать срок действия при создании периодических расписаний. При достижении срока действия расписания будут отключены.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Отключение расписание на классическом портале Azure
Отключить расписание на классическом портале Azure можно на странице "Сведения о расписании" для соответствующего расписания.

1. На классическом портале Azure щелкните "Служба автоматизации", а затем — имя учетной записи службы автоматизации.
2. Перейдите на вкладку "Средства".
3. Щелкните имя расписания и откройте страницу со сведениями о нем.
4. Задайте значение **Нет** для параметра **Включено**.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Отключение расписания на портале Azure
1. На портале Azure из учетной записи службы автоматизации щелкните элемент **Ресурсы**, чтобы открыть колонку **Ресурсы**.
2. Щелкните элемент **Расписания**, чтобы открыть колонку **Расписания**.
3. Щелкните имя расписания и откройте колонку со сведениями о нем.
4. Задайте значение **Нет** для параметра **Включено**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Отключение расписания с помощью Windows PowerShell
В службе автоматизации Azure вы можете использовать командлет [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx), чтобы изменить свойства существующего расписания для классического модуля Runbook, или командлет [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx), чтобы изменить расписание для модулей Runbook на портале Azure. Чтобы отключить расписание, задайте значение **False** для параметра **IsEnabled**.

Приведенные ниже примеры команд демонстрируют, как отключить расписание, используя командлет управления службами Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

Приведенные ниже примеры команд демонстрируют, как отключить расписание для Runbook, используя командлет Azure Resource Manager.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о работе с расписаниями см. в статье [Расписания в службе автоматизации Azure](http://msdn.microsoft.com/library/azure/dn940016.aspx).
* Сведения о том, как начать работу с модулями Runbook в службе автоматизации Azure, см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md). 




<!--HONumber=Nov16_HO3-->


