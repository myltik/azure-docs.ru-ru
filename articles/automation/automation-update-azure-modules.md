---
title: "Обновление модулей Azure в службе автоматизации Azure | Документация Майкрософт"
description: "В этой статье описывается, как можно обновить общие модули Azure PowerShell, предоставленные по умолчанию в службе автоматизации Azure."
services: automation
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: ed8c97b642d406a05817ec6c67f31a1b4bce93b0
ms.contentlocale: ru-ru
ms.lasthandoff: 06/14/2017


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Как обновить модули Azure PowerShell в службе автоматизации Azure

Наиболее распространенные модули Azure PowerShell приведены в каждой учетной записи автоматизации по умолчанию.  Группа разработчиков Azure регулярно обновляет модули Azure, поэтому в учетной записи службы автоматизации мы предоставляем способ обновления модулей в учетной записи, когда на портале доступны новые версии.  

Так как модули регулярно обновляются специалистами группы продуктов, то изменения могут затронуть и входящие в них командлеты, что может негативно повлиять на работу модулей Runbook. Это зависит от типа изменения. Например, возможно переименование параметра или полное прекращение поддержки одного из командлетов. Чтобы избежать негативного влияния на модули Runbook и автоматизируемые ими процессы, настоятельно рекомендуется выполнить предварительное тестирование и проверку.  Если у вас нет выделенной учетной записи службы автоматизации, предназначенной для этой цели, то рекомендуется ее создать, чтобы протестировать различные сценарии и перестановки в процессе разработки модулей Runbook, а также повторяющиеся изменения, такие как обновления модулей PowerShell.  После проверки результатов и применения необходимых изменений можно перейти к процессу переноса измененных модулей Runbook и выполнить описанное ниже обновление в рабочей среде.     

## <a name="updating-azure-modules"></a>Обновление модулей Azure

1. В колонке"Модули" вашей учетной записи службы автоматизации есть параметр под названием **Update Azure Modules** (Обновление модулей Azure).  Он всегда включен.<br><br> ![Параметр Update Azure Modules (Обновление модулей Azure) в колонке "Модули"](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Щелкните **Update Azure Modules** (Обновление модулей Azure), после чего появится уведомление с запросом о продолжении.<br><br> ![Уведомление об обновлении модулей Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Щелкните **Да**, чтобы начать процесс обновления модулей.  Процесс обновления следующих модулей занимает около 15–20 минут:

  * Таблицы Azure
  * Azure.Storage;
  * AzureRm.Automation;
  * AzureRm.Compute;
  * AzureRm.Profile;
  * AzureRm.Resources;
  * AzureRm.Sql;
  * AzureRm.Storage.

    Если модули уже обновлены, процесс будет завершен через несколько секунд.  После завершения процесса обновления вы получите уведомление.<br><br> ![Состояние обновления Update Azure Modules (Обновление модулей Azure)](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> При запуске нового запланированного задания служба автоматизации Azure будет использовать модули последней версии в вашей учетной записи службы автоматизации.    

При использовании командлетов из этих модулей Azure PowerShell в модулях runbook для управления ресурсами Azure этот процесс обновления необходимо выполнять примерно каждый месяц, чтобы гарантировать наличие последних модулей.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о модулях интеграции и создании настраиваемых модулей для дальнейшей интеграции службы автоматизации с другими системами, службами и решениями см. в статье [Модули интеграции службы автоматизации Azure](automation-integration-modules.md).

* Рассмотрите возможность интеграции системы управления версиями с помощью [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) или [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md), чтобы централизованно управлять выпусками своих модулей Runbook службы автоматизации и набором конфигураций.  
