---
title: "Интеграция службы автоматизации Azure с системой управления версиями Visual Studio Team Services | Документация Майкрософт"
description: "В этой статье описан сценарий настройки интеграции учетной записи службы автоматизации Azure с системой управления версиями Visual Studio Team Services."
services: automation
documentationcenter: 
author: eamono
manager: 
editor: 
keywords: "azure powershell, VSTS, система управления версиями, служба автоматизации"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.openlocfilehash: 01f9c01c9e04e02dbb548b68cf99684ba6ddd57e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-visual-studio-team-services"></a>Сценарий службы автоматизации Azure: интеграция системы управления версиями службы автоматизации с Visual Studio Team Services

В этом сценарии показан проект Visual Studio Team Services, который используется для управления модулями Runbook службы автоматизации Azure или конфигурациями DSC в системе управления версиями.
В этой статье описывается процесс интеграции VSTS со средой службы автоматизации Azure, чтобы при каждом возврате происходила непрерывная интеграция.

## <a name="getting-the-scenario"></a>Получение сценария

В сценарии используются два модуля Runbook PowerShell, которые можно импортировать непосредственно из [коллекции модулей Runbook](automation-runbook-gallery.md) на портале Azure или скачать из [коллекции PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Модули Runbook

Модуль Runbook | Описание| 
--------|------------|
Sync-VSTS | Импорт модулей Runbook или конфигураций из системы управления версиями VSTS, когда выполняется возврат. Если запустить модуль вручную, то он импортирует и опубликует все модули Runbook или конфигурации в учетную запись службы автоматизации.| 
Sync-VSTSGit | Импорт модулей Runbook или конфигураций из системы управления версиями Git VSTS, когда выполняется возврат. Если запустить модуль вручную, то он импортирует и опубликует все модули Runbook или конфигурации в учетную запись службы автоматизации.|

### <a name="variables"></a>Переменные

Переменная | Описание|
-----------|------------|
VSToken | Создаваемый вами ресурс защищенной переменной, который содержит персональный маркер доступа VSTS. Сведения о создании персонального маркера доступа VSTS см. в статье [Basic authentication for the REST APIs](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview) (Обычная проверка подлинности для интерфейсов REST API). 
## <a name="installing-and-configuring-this-scenario"></a>Установка и настройка данного сценария

Создайте [персональный маркер доступа](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview) в VSTS, который будет использоваться для синхронизации модулей Runbook или конфигураций в учетную запись службы автоматизации.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Создайте [защищенную переменную](automation-variables.md) в учетной записи службы автоматизации для хранения персонального маркера доступа, чтобы модуль Runbook мог пройти проверку подлинности в VSTS и синхронизировать модули Runbook или конфигурации в учетную запись службы автоматизации. Можно назвать ее VSToken. 

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Импортируйте модуль Runbook, который синхронизирует ваши модули Runbook или конфигурации в учетную запись службы автоматизации. Можно использовать [пример модуля Runbook VSTS](https://www.powershellgallery.com/packages/Sync-VSTS/1.0/DisplayScript) или [пример модуля Runbook VSTS Git] (https://www.powershellgallery.com/packages/Sync-VSTSGit/1.0/DisplayScript) с сайта PowerShellGallery.com в зависимости от того, какая система управления версиями используется (VSTS или VSTS Git), и развернуть в учетной записи службы автоматизации.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Теперь можно [опубликовать](automation-creating-importing-runbook.md#publishing-a-runbook) этот модуль Runbook, чтобы затем создать объект webhook. 
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Создайте объект [webhook](automation-webhooks.md) для этого модуля Runbook Sync-VSTS и заполните параметры, как показано ниже. Скопируйте URL-адрес webhook, так как он вам понадобится для перехватчика события в VSTS. VSAccessTokenVariableName — это имя (VSToken) защищенной переменной, которую вы создали ранее для хранения персонального маркера доступа. 

При интеграция с помощью VSTS (Sync-VSTS.ps1) используются следующие параметры.
### <a name="sync-vsts-parameters"></a>Параметры Sync-VSTS

Параметр | Описание| 
--------|------------|
WebhookData | Будет содержать сведения о возврате, отправленные из перехватчика события VSTS. Этот параметр следует оставить пустым.| 
ResourceGroup | Имя группы ресурсов, в которой находится учетная запись службы автоматизации.|
AutomationAccountName | Имя учетной записи службы автоматизации, которая будет синхронизироваться с VSTS.|
VSFolder | Имя папки в VSTS, где находятся модули Runbook и конфигурации.|
VSAccount | Имя учетной записи Visual Studio Team Services.| 
VSAccessTokenVariableName | Имя защищенной переменной (VSToken), в который хранится персональный маркер доступа VSTS.| 


![](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

При интеграция с помощью VSTS GIT (Sync-VSTSGit.ps1) используются следующие параметры.

Параметр | Описание|
--------|------------|
WebhookData | Будет содержать сведения о возврате, отправленные из перехватчика события VSTS. Этот параметр следует оставить пустым.| ResourceGroup | Имя группы ресурсов, в которой находится учетная запись службы автоматизации.|
AutomationAccountName | Имя учетной записи службы автоматизации, которая будет синхронизироваться с VSTS.|
VSAccount | Имя учетной записи Visual Studio Team Services.|
VSProject | Имя проекта в VSTS, где находятся модули Runbook и конфигурации.|
GitRepo | Имя репозитория Git.|
GitBranch | Имя ветви в репозитории VSTS Git.|
Папка | Имя папки в ветви VSTS Git.|
VSAccessTokenVariableName | Имя защищенной переменной (VSToken), в который хранится персональный маркер доступа VSTS.|

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Создайте перехватчик события в VSTS для операций возврата в папку, который запускает этот webhook при возврате кода. Выберите webhook как службу для интеграции при создании подписки. Дополнительные сведения о перехватчиках события см. в статье [Integrate with service hooks](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/get-started) (Интеграция с перехватчиками события).
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Теперь вы можете выполнять все операции возврата модулей Runbook и конфигураций в VSTS, и они будут автоматически синхронизироваться в учетную запись службы автоматизации.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Если этот модуль Runbook запускается вручную, а не активируется VSTS, то параметр WebhookData можно оставить пустым, и он выполнит полную синхронизацию из указанной папки VSTS.

Если вы хотите удалить сценарий, то удалите перехватчик события из VSTS, удалите модуль Runbook и переменную VSToken.
