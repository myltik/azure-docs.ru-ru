---
title: Параметры модуля runbook в службе автоматизации Azure
description: Описываются параметры конфигурации для модуля runbook службы автоматизации Azure и объясняется, как изменить их с помощью портала Azure и Windows PowerShell.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 951e59333ab1a1e982386c5c71f79b86f5e62440
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194202"
---
# <a name="runbook-settings"></a>Параметры модуля Runbook
Каждый модуль Runbook в службе автоматизации Azure имеет несколько параметров, которые помогут определить его и изменить его поведение при ведении журнала. Каждый из этих параметров описан ниже, рядом с ним приводятся пояснения об изменении его значения.

## <a name="settings"></a>Параметры
### <a name="name-and-description"></a>Имя и описание
Имя модуля Runbook нельзя изменить после его создания. Описание не обязательно и может содержать до 512 символов.

### <a name="tags"></a>Теги
Теги позволяют назначать отдельные слова и фразы для идентификации модуля Runbook. Например, при отправке модуля Runbook в [коллекцию PowerShell](https://www.powershellgallery.com/) можно указать определенные теги для идентификации категорий, к которым должен относиться модуль Runbook. Вы можете указать несколько тегов модуля Runbook, разделив их запятой.

### <a name="logging"></a>Ведение журналов
По умолчанию записи Verbose и Progress не записываются в журнал заданий. Вы можете изменить параметры для определенного модуля Runbook, чтобы записывать эти записи. Дополнительные сведения об этих записях см. в статье [Выходные данные и сообщения Runbook в службе автоматизации Azure](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Изменение параметров модуля Runbook

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Изменение параметров модуля Runbook с помощью портала Azure
Параметры модуля Runbook можно изменить на портале Azure в колонке **Параметры** модуля Runbook.

1. На портале Azure щелкните **Служба автоматизации** и затем имя учетной записи службы автоматизации.
2. Выберите вкладку **Модули Runbook** .
3. Щелкните имя модуля Runbook. Откроется колонка "Параметры" выбранного модуля. В ней вы можете указать или изменить теги, ввести описание модуля Runbook, настроить параметры ведения журнала и трассировки, а также получить доступ к средствам поддержки, которые помогут устранить возникшие проблемы.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Изменение параметров модуля Runbook с помощью Windows PowerShell
Для изменения параметров модуля Runbook можно воспользоваться командлетом [Set-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx). Если вы хотите указать несколько тегов, вы можете предоставить массив или единую строку со значениями, разделенными запятыми, в параметр Tags. С помощью командлета [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx) можно получить текущие теги.

Приведенные образцы команд показывают, как установить свойства для модуля Runbook. В этом примере к существующим тегам добавляются три тега и указывается, что необходимо вести запись подробных записей.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>Дополнительная информация
* Чтобы узнать, как создавать и извлекать выходные данные и сообщения об ошибках из модулей Runbook, ознакомьтесь со статьей [Выходные данные и сообщения Runbook в службе автоматизации Azure](automation-runbook-output-and-messages.md) 
* Сведения о добавлении модуля Runbook, который уже был разработан сообществом или взят из другого источника, а также сведения о создании собственного модуля Runbook см. в статье [Создание или импорт модуля Runbook](automation-creating-importing-runbook.md). 

