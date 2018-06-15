---
title: Создание или импорт модуля Runbook в службе автоматизации Azure
description: В этой статье описывается создание новых модулей Runbook в службе автоматизации Azure или их импорт из файла.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ea03f34a2e709fe6f6d8d2f7e13798cf6dcd1e34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598248"
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Создание или импорт модуля Runbook в службе автоматизации Azure
Чтобы добавить модуль Runbook в службу автоматизации Azure, можно [создать новый модуль](#creating-a-new-runbook) или импортировать уже существующий модуль из файла или из [коллекции Runbook](automation-runbook-gallery.md). В этой статье рассказывается, как создавать и импортировать модули Runbook из файла.  Информацию о получении доступа к модулям Runbook сообществ см. в статье [Коллекции модулей Runbook и других модулей для службы автоматизации Azure](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Создание нового модуля Runbook
Создать новый модуль в службе автоматизации Azure можно с помощью одного из порталов Azure или Windows PowerShell. Вновь созданный модуль Runbook можно изменить, следуя инструкциям в статьях [Изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md) и [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Создание нового модуля Runbook в службе автоматизации Azure с помощью портала Azure
1. На портале Azure выберите свою учетную запись службы автоматизации.
2. В концентраторе выберите **Модули Runbook**, чтобы открыть список модулей Runbook.
3. Нажмите кнопку **Добавить Runbook**, а затем **Создать новый Runbook**.
4. Введите **имя** для модуля Runbook и выберите его [тип](automation-runbook-types.md). Имя модуля Runbook должно начинаться с буквы и содержать буквы, цифры, символы подчеркивания и дефисы.
5. Щелкните **Создать** , чтобы создать модуль Runbook и открыть текстовый редактор.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Создание модуля Runbook в службе автоматизации Azure с помощью Windows PowerShell
Командлет [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) позволяет создать пустой [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Вы можете указать параметр **Имя**, чтобы создать пустой модуль Runbook, который впоследствии можно будет изменить, или указать параметр **Путь**, чтобы импортировать файл Runbook. Параметр **Тип** также должен быть включен для указания одного из четырех типов Runbook.

Команды в приведенном ниже примере демонстрируют создание пустого модуля Runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Импорт модуля из файла в службу автоматизации Azure
Для создания модуля Runbook в службе автоматизации Azure можно импортировать сценарий или рабочий процесс PowerShell (с расширением PS1) либо экспортировать графический модуль Runbook (с расширением GRAPHRUNBOOK) или скрипт Python 2 (с расширением PY).  При этом необходимо указать [тип модуля Runbook](automation-runbook-types.md), который создается во время импорта, с учетом следующих рекомендаций.

* Файл GRAPHRUNBOOK может быть импортирован только в новый [графический модуль Runbook](automation-runbook-types.md#graphical-runbooks), а графические модули Runbook могут быть созданы только из файла GRAPHRUNBOOK.
* Файл PS1 с рабочим процессом PowerShell можно импортировать только в [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Если файл содержит несколько рабочих процессов PowerShell, импорт завершится ошибкой. Каждый рабочий процесс необходимо сохранить в отдельный файл и импортировать отдельно.
* Файл PS1 без рабочего процесса можно импортировать либо в [модуль Runbook PowerShell](automation-runbook-types.md#powershell-runbooks), либо в [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Если файл импортируется в модуль Runbook рабочего процесса PowerShell, он преобразуется в рабочий процесс, а в модуль Runbook включаются комментарии с описанием внесенных изменений.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Импорт модуля Runbook из файла с помощью портала Azure
Для импорта файла сценария в службу автоматизации Azure можно использовать описанную ниже процедуру.  

> [!NOTE]
> Обратите внимание, что с помощью портала PS1-файл можно импортировать только в модуль Runbook рабочего процесса PowerShell.
> 
> 

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. В концентраторе выберите **Модули Runbook**, чтобы открыть список модулей Runbook.
3. Нажмите кнопку **Добавить Runbook**, а затем **Импорт**.
4. Щелкните **файл модуля Runbook** и выберите файл для импорта.
5. Если поле **Имя** активно, его можно изменить.  Имя модуля Runbook должно начинаться с буквы и содержать буквы, цифры, символы подчеркивания и дефисы.
6. [Тип Runbook](automation-runbook-types.md) выбирается автоматически, но его можно изменить, учитывая применимые ограничения. 
7. Новый модуль Runbook появится в списке модулей Runbook для учетной записи службы автоматизации.
8. Перед запуском модуля его необходимо [опубликовать](#publishing-a-runbook) .

> [!NOTE]
> После импорта графического модуля Runbook или графического модуля Runbook рабочего процесса PowerShell появляется возможность, при необходимости, преобразования модуля в другой тип. Выполнить преобразование в текстовый модуль Runbook невозможно.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Импорт модуля Runbook из файла сценария с помощью Windows PowerShell
Чтобы импортировать файл сценария как черновик модуля Runbook рабочего процесса PowerShell, можно воспользоваться командлетом [Import-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx). Если модуль Runbook уже существует, импорт завершится ошибкой. Чтобы этого не произошло, необходимо использовать параметр *-Force*. 

Приведенные ниже примеры команд демонстрируют импорт файла сценария в модуль Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="publishing-a-runbook"></a>Публикация модуля Runbook
Перед запуском вновь созданного или импортированного модуля Runbook его необходимо опубликовать.  У каждого Runbook в службе автоматизации есть черновая и опубликованная версия. Запустить можно только опубликованную версию, а изменить — только черновую. Изменения, внесенные в черновик, не влияют на опубликованную версию. Если требуется черновая версия, ее можно опубликовать, заменив опубликованную версию черновой.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Публикация модуля Runbook с помощью портала Azure
1. Откройте модуль Runbook на портале Azure.
2. Нажмите кнопку **Edit** (Изменить).
3. Нажмите кнопку **Опубликовать**, а в проверочном сообщении — кнопку **Да**.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Публикация модуля Runbook с помощью Windows PowerShell
Командлет [Publish-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) позволяет опубликовать модуль Runbook с помощью Windows PowerShell. Команды в приведенном ниже примере показывают, как опубликовать образец модуля Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о преимуществах использования коллекции модулей Runbook и PowerShell см. в статье [Коллекции модулей Runbook и других модулей для службы автоматизации Azure](automation-runbook-gallery.md).
* Дополнительные сведения о редактировании модулей Runbook PowerShell и рабочих процессов PowerShell с помощью текстового редактора см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md).
* Дополнительные сведения о графической разработке модулей Runbook см. в статье [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).

