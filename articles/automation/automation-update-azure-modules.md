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
ms.date: 04/20/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 6765ea93dd4e4e2594fb147dd19120aec058a2f5
ms.lasthandoff: 04/21/2017


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Как обновить модули Azure PowerShell в службе автоматизации Azure

Наиболее распространенные модули Azure PowerShell приведены в каждой учетной записи автоматизации по умолчанию.  Группа разработчиков Azure регулярно обновляет модули Azure, поэтому в учетной записи службы автоматизации мы предоставляем способ обновления модулей в учетной записи, когда на портале доступны новые версии.  

## <a name="updating-azure-modules"></a>Обновление модулей Azure

1. В колонке"Модули" вашей учетной записи службы автоматизации есть параметр под названием **Update Azure Modules** (Обновление модулей Azure).  Он всегда включен.<br><br> ![Параметр Update Azure Modules (Обновление модулей Azure) в колонке "Модули"](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Щелкните **Update Azure Modules** (Обновление модулей Azure), после чего появится уведомление с запросом о продолжении.<br><br> ![Уведомление об обновлении модулей Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Щелкните **Да**, чтобы начать процесс обновления модулей.  Процесс обновления следующих модулей занимает около 15–20 минут:

  * Таблицы Azure
  *    Azure.Storage;
  *    AzureRm.Automation;
  *    AzureRm.Compute;
  *    AzureRm.Profile;
  *    AzureRm.Resources;
  *    AzureRm.Sql;
  * AzureRm.Storage.

    Если модули уже обновлены, процесс будет завершен через несколько секунд.  После завершения процесса обновления вы получите уведомление.<br><br> ![Состояние обновления Update Azure Modules (Обновление модулей Azure)](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

Каждый раз при создании расписания все последующие задания, выполняющиеся по нему, используют модули в учетной записи автоматизации, актуальные на момент создания расписания.  Чтобы использовать обновленные модули с запланированными runbook, нужно разорвать и затем восстановить связь расписания с этим модулем runbook.   

При использовании командлетов из этих модулей Azure PowerShell в модулях runbook для управления ресурсами Azure этот процесс обновления необходимо выполнять примерно каждый месяц, чтобы гарантировать наличие последних модулей.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модулях интеграции и создании настраиваемых модулей для дальнейшей интеграции службы автоматизации с другими системами, службами и решениями см. в статье [Модули интеграции службы автоматизации Azure](automation-integration-modules.md).
