---
title: Обзор DSC службы автоматизации Azure
description: Обзор DSC службы автоматизации Azure, условий использования и известных проблем
keywords: PowerShell DSC, настройка требуемого состояния, PowerShell DSC для Azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 83d5b89422a0181c06dbfe3b2bd8975ef7214b9d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193263"
---
# <a name="azure-automation-dsc-overview"></a>Обзор DSC службы автоматизации Azure

Azure Automation DSC — это служба Azure, которая позволяет создавать и компилировать [конфигурации](https://msdn.microsoft.com/powershell/dsc/configurations) Desired State Configuration (DSC) PowerShell, управлять ими, импортировать [ресурсы DSC](https://msdn.microsoft.com/powershell/dsc/resources), а также назначать конфигурации целевым узлам, и все это в облаке.

## <a name="why-use-azure-automation-dsc"></a>Преимущества Azure Automation DSC

Azure Automation DSC обеспечивает ряд преимуществ по сравнению с использованием DSC за пределами Azure.

### <a name="built-in-pull-server"></a>Встроенный опрашивающий сервер

Служба автоматизации Azure предоставляет [опрашивающий сервер DSC](https://msdn.microsoft.com/powershell/dsc/pullserver), чтобы целевые узлы могли автоматически получать конфигурации, переходить в требуемое состояние и сообщать о готовности.
Встроенный опрашивающий сервер в службе автоматизации Azure избавляет от необходимости устанавливать и обслуживать собственный опрашивающий сервер.
Служба автоматизации Azure может работать с виртуальными и физическими машинами под управлением Windows или Linux, размещенными в облаке или локально.

### <a name="management-of-all-your-dsc-artifacts"></a>Управление всеми артефактами DSC

Azure Automation DSC обеспечивает тот же слой управления для [Desired State Configuration PowerShell](https://msdn.microsoft.com/powershell/dsc/overview), что и служба автоматизации Azure предлагает для сценариев PowerShell.

Вы можете управлять всеми конфигурациями, ресурсами и целевыми узлами DSC с помощью портала Azure или PowerShell.

![Снимок экрана колонки службы автоматизации Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Импорт данных отчетов в Log Analytics

Узлы, которые управляются с помощью Azure Automation DSC, отправляют на встроенный опрашивающий сервер подробные отчеты с данными о состоянии.
В Azure Automation DSC можно настроить отправку этих данных в рабочую область Log Analytics.
Сведения об отправке данных о состоянии DSC в рабочую область Log Analytics см. в статье [Пересылка данных отчетов Azure Automation DSC в Log Analytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Видео: общие сведения

Предпочитаете смотреть, а не читать? Посмотрите следующий видеоролик, выпущенный в мае 2015 г., когда впервые было объявлено о создании службы Azure Automation DSC.

>[!NOTE]
>Несмотря на то, что основные концепции и жизненный цикл, о которых рассказывается в этом видеоролике, по-прежнему актуальны, с момента записи этого видеоролика Azure Automation DSC сделала большой шаг вперед.
>Теперь она стала общедоступной, оснащена более функциональным пользовательским интерфейсом на портале Azure и поддерживает множество дополнительных возможностей.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Дополнительная информация

* Сведения о развертывании узлов для управления с помощью Azure Automation DSC см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).
* Чтобы начать работу с Azure Automation DSC, см. статью [Приступая к работе с Azure Automation DSC](automation-dsc-getting-started.md).
* Сведения о компиляции конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компиляция конфигураций в Azure Automation DSC](automation-dsc-compile.md).
* Справочник по командлетам PowerShell для Azure Automation DSC приводится в статье [Azure​RM.​Automation](/powershell/module/azurerm.automation/#automation).
* Сведения о ценах см. на [странице с ценами на Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/).
* Пример использования DSC службы автоматизации Azure в конвейере непрерывного развертывания см. в статье [Пример использования. Непрерывное развертывание на виртуальных машинах с помощью Automation DSC и Chocolatey](automation-dsc-cd-chocolatey.md).
