<properties 
	pageTitle="Создание или импорт модуля Runbook в службе автоматизации Azure"
	description="В этой статье описывается создание новых модулей Runbook в службе автоматизации Azure или их импорт из файла."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/22/2015"
	ms.author="bwren" />

# Создание или импорт модуля Runbook в службе автоматизации Azure

Чтобы добавить модуль Runbook в службу автоматизации Azure, можно [создать новый модуль](#creating-a-new-runbook) или импортировать уже существующий модуль из файла или из [галереи Runbook](automation-runbook-gallery.md). В этой статье рассказывается, как создавать и импортировать модули Runbook из файла. Информацию о получении доступа к модулям Runbook сообществ см. в статье [Runbook и коллекции модулей для службы автоматизации Azure](automation-runbook-gallery.md).

## Создание нового модуля Runbook

Создать новый модуль в службе автоматизации Azure можно с помощью одного из порталов Azure или Windows PowerShell. Вновь созданный модуль Runbook можно изменить, следуя инструкциям в статьях [Изучение рабочего процесса PowerShell](automation-powershell-workflow.md) и [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md).

### Создание нового модуля Runbook в службе автоматизации Azure с помощью портала Azure

На портале Azure можно работать только с [модулями Runbook рабочих процессов PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

1. На портале Azure выберите параметры **Создать**, **Службы приложений**, **Автоматизация**, **Runbook**, **Быстрое создание**.
2. Введите необходимые сведения и нажмите кнопку **Создать**. Имя модуля Runbook должно начинаться с буквы и содержать буквы, цифры, символы подчеркивания и дефисы.
3. Если вы хотите изменить модуль Runbook сейчас, нажмите кнопку **Изменить Runbook**. В противном случае нажмите кнопку **ОК**.
4. Новый модуль Runbook появится на вкладке **Модули Runbook**.


### Создание модуля Runbook в службе автоматизации Azure с помощью портала предварительной версии Azure

1. Откройте свою учетную запись в службе автоматизации на портале предварительной версии Azure. 
2. Щелкните элемент **Модули Runbook**, чтобы открыть список модулей Runbook.
3. Нажмите кнопку **Добавить Runbook**, а затем **Создать модуль Runbook**.
2. Введите **имя** для модуля Runbook и выберите его [тип](automation-runbook-types.md). Имя модуля Runbook должно начинаться с буквы и содержать буквы, цифры, символы подчеркивания и дефисы.
3. Щелкните **Создать**, чтобы создать модуль Runbook и открыть текстовый редактор.


### Создание модуля Runbook в службе автоматизации Azure с помощью Windows PowerShell

Командлет [New-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690272.aspx) позволяет создать пустой [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Вы можете указать параметр **Имя**, чтобы создать пустой модуль Runbook, который впоследствии можно будет изменить, или указать параметр **Путь**, чтобы импортировать файл сценария.

Команды в приведенном ниже примере демонстрируют создание пустого модуля Runbook.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    New-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName 

## Импорт модуля из файла в службу автоматизации Azure

Для создания модуля Runbook в службе автоматизации Azure можно импортировать сценарий или рабочий процесс PowerShell (с расширением PS1) или экспортировать графический модуль Runbook (с расширением GRAPHRUNBOOK). При этом необходимо указать [тип модуля Runbook](automation-runbook-types.md), который будет создан в результате импорта, с учетом следующих рекомендаций.

- Файл GRAPHRUNBOOK может быть импортирован только в новый [графический модуль Runbook](automation-runbook-types.md#graphical-runbooks), а графические модули Runbook могут быть созданы только из файла GRAPHRUNBOOK.
- Файл PS1 с рабочим процессом PowerShell можно импортировать только в [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Если файл содержит несколько рабочих процессов PowerShell, импорт завершится ошибкой. Каждый рабочий процесс необходимо сохранить в отдельный файл и импортировать отдельно.
- Файл PS1 без рабочего процесса можно импортировать либо в [ модуль Runbook PowerShell](automation-runbook-types.md#powershell-runbooks), либо в [модуль Runbook рабочего процесса PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Если файл импортируется в модуль Runbook рабочего процесса PowerShell, он преобразуется в рабочий процесс, а в модуль Runbook включаются комментарии с описанием внесенных изменений.

### Импорт модуля Runbook из файла с помощью портала Azure
Для импорта файла сценария в службу автоматизации Azure можно использовать описанную ниже процедуру. Обратите внимание, что с помощью этого портала файл PS1 можно импортировать только в модуль Runbook рабочего процесса PowerShell. Для других типов модулей используйте портал предварительной версии Azure.

1. На портале управления Azure выберите **Службу автоматизации**, а затем учетную запись службы автоматизации.
2. Щелкните **Импорт**.
3. Щелкните **Поиск файла** и найдите файл сценария, который нужно импортировать.
4. Если вы хотите изменить модуль Runbook сейчас, нажмите кнопку **Изменить Runbook**. В противном случае нажмите кнопку «ОК».
5. Созданный модуль Runbook появится на вкладке **Модули Runbook** учетной записи службы автоматизации.
6. Перед запуском модуля его необходимо [опубликовать](#publishing-a-runbook).


### Импорт модуля Runbook из файла с помощью портала предварительной версии Azure
Для импорта файла сценария в службу автоматизации Azure можно использовать описанную ниже процедуру. Обратите внимание, что с помощью этого портала файл PS1 можно импортировать только в модуль Runbook рабочего процесса PowerShell.

1. Откройте свою учетную запись в службе автоматизации на портале предварительной версии Azure. 
2. Щелкните элемент **Модули Runbook**, чтобы открыть список модулей Runbook.
3. Нажмите кнопку **Добавить Runbook**, а затем **Импорт**.
4. Щелкните **файл модуля Runbook** и выберите файл для импорта.
2. Если поле **Имя** активно, его можно изменить. Имя модуля Runbook должно начинаться с буквы и содержать буквы, цифры, символы подчеркивания и дефисы.
3. Выберите [тип модуля Runbook](automation-runbook-types.md) с учетом указанных выше ограничений.
3. Новый модуль Runbook появится в списке модулей Runbook для учетной записи автоматизации.
4. Перед запуском модуля его необходимо [опубликовать](#publishing-a-runbook).

### Импорт модуля Runbook из файла сценария с помощью Windows PowerShell

Командлет [Set-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690267.aspx) позволяет импортировать файл сценария в черновую версию существующего модуля Runbook. Файл сценария должен содержать один рабочий процесс Windows PowerShell. Если у модуля Runbook уже есть черновая версия и параметр перезаписи не используется, импорт завершится ошибкой. Импортированный модуль Runbook можно опубликовать с помощью команды [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx).

Приведенные ниже примеры команд демонстрируют импорт файла сценария в существующий модуль Runbook и публикацию этого модуля.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition –AutomationAccountName $automationAccountName –Name $runbookName –Path $ scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName


## Публикация модуля Runbook

Перед запуском вновь созданного или импортированного модуля Runbook его необходимо опубликовать. У каждого Runbook в службе автоматизации Azure есть черновая и опубликованная версия. Запустить можно только опубликованную версию, а изменить — только черновую. Изменения, внесенные в черновик, не влияют на опубликованную версию. Если требуется черновая версия, ее можно опубликовать, заменив опубликованную версию черновой.

## Публикация модуля Runbook с помощью портала Azure

1. Откройте модуль Runbook на портале Azure.
1. В верхней части экрана щелкните **Автор**.
1. В нижней части экрана щелкните **Опубликовать**, а в проверочном сообщении нажмите кнопку **Да**.

## Публикация модуля Runbook с помощью портала предварительной версии Azure

1. Откройте модуль Runbook на портале предварительной версии Azure.
1. Нажмите кнопку **Edit** (Изменить).
1. Нажмите кнопку **Опубликовать**, а в проверочном сообщении — кнопку **Да**.


## Публикация модуля Runbook с помощью Windows PowerShell

Командлет [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx) позволяет опубликовать модуль Runbook с помощью Windows PowerShell. Команды в приведенном ниже примере показывают, как опубликовать образец модуля Runbook.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	
	Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName



## Связанные статьи

- [Runbook и коллекции модулей для службы автоматизации Azure](automation-runbook-gallery.md)
- [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md)
- [Графическая разработка в службе автоматизации Azure](automation-graphical-authoring-intro.md)

<!---HONumber=Oct15_HO1-->