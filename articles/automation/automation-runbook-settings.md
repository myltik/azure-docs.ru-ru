---
title: Параметры модуля Runbook
description: Описываются параметры конфигурации для модуля Runbook службы автоматизации Azure и то, как изменить их с помощью портала управления Azure и Windows PowerShell.
services: automation
documentationcenter: ''
author: bwren
manager: stevenka
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: bwren

---
# Параметры модуля Runbook
Каждый модуль Runbook в службе автоматизации Azure имеет несколько параметров, которые помогут определить его и изменить его поведение при ведении журнала. Каждый из этих параметров описан ниже, рядом с ним приводятся пояснения об изменении его значения.

## Параметры
### Имя и описание
Имя модуля Runbook нельзя изменить после его создания. Описание не обязательно и может содержать до 512 символов.

### Теги
Теги позволяют назначать отдельные слова и фразы для идентификации модуля Runbook. Например, при отправке модуля Runbook в [коллекцию модулей Runbook](https://msdn.microsoft.com/library/dn781422.aspx) можно указать определенные теги для идентификации категорий, к которым должен относиться модуль Runbook. Вы можете указать несколько тегов модуля Runbook, разделив их запятой.

### Ведение журналов
По умолчанию записи Verbose и Progress не записываются в журнал заданий. Вы можете изменить параметры для определенного модуля Runbook, чтобы записывать эти записи. Дополнительные сведения об этих записях см. в разделе [Выходные данные модуля Runbook и сообщения](https://msdn.microsoft.com/library/dn879148.aspx).

## Изменение параметров модуля Runbook
### Изменение параметров модуля Runbook с помощью портала управления Azure
Параметры модуля Runbook можно изменить на портале управления Azure на странице **Настройка** модуля Runbook.

1. На портале управления Azure выберите **Служба автоматизации** и затем имя учетной записи службы автоматизации.
2. Выберите вкладку **Модули Runbook**.
3. Щелкните имя модуля Runbook.
4. Перейдите на вкладку **Настройка**.

### Изменение параметров модуля Runbook с помощью Windows PowerShell
Можно использовать командлет [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) для изменения параметров модуля Runbook. Если вы хотите указать несколько тегов, вы можете предоставить массив или единую строку со значениями, разделенными запятыми, в параметр Tags. Можно получить текущие теги при помощи командлета [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx).

Приведенные образцы команд показывают, как установить свойства для модуля Runbook. В этом примере к существующим тегам добавляются три тега и указывается, что необходимо вести запись подробных записей.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## Связанные статьи
* [Выходные данные Runbook и сообщения](automation-runbook-output-and-messages.md) 
* [Создание или импорт модуля Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 

<!---HONumber=AcomDC_0211_2016-->