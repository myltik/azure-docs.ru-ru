---
title: Вертикальное масштабирование масштабируемых наборов виртуальных машин Azure | Документация Майкрософт
description: Сведения о вертикальном масштабировании виртуальной машины в ответ на оповещения c помощью службы автоматизации Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: negat
ms.openlocfilehash: 6e4733e023d1dc27fb099216f9afea07fe07446c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781825"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Вертикальное автомасштабирование масштабируемых наборов виртуальных машин
В этой статье описывается, как выполнить вертикальное масштабирование [масштабируемых наборов виртуальных машин](https://azure.microsoft.com/services/virtual-machine-scale-sets/) Azure с повторной подготовкой или без нее. Дополнительные сведения о вертикальном масштабировании виртуальных машин, которые не входят в масштабируемые наборы, с помощью службы автоматизации Azure см. в [этой статье](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Вертикальное масштабирование — это изменение размеров виртуальной машины (*увеличение масштаба* или его *уменьшение*) в ответ на рабочую нагрузку. Сравните это с [горизонтальным масштабированием](virtual-machine-scale-sets-autoscale-overview.md) (также называется *развертыванием* и *свертыванием* количества экземпляров), при котором в зависимости от рабочей нагрузки изменяется количество виртуальных машин.

Повторная подготовка означает удаление существующей виртуальной машины и ее замену новой. Когда вы увеличиваете или уменьшаете размер виртуальных машин в масштабируемом наборе, как правило, нужно либо изменить размер имеющихся виртуальных машин и сохранить данные, либо развернуть новые виртуальные машины другого размера. В этом документе описываются оба сценария.

Когда полезно выполнять вертикальное масштабирование:

* Если служба, работающая на базе виртуальных машин, мало используется (например, в выходные). Уменьшение размера виртуальной машины может сократить ежемесячные расходы.
* Чтобы увеличить размер виртуальной машины в соответствии с возросшими потребностями, не создавая дополнительные виртуальные машины.

Вы можете настроить активацию вертикального масштабирования с помощью оповещений на основе метрик масштабируемого набора виртуальных машин. Активированное оповещение запускает веб-перехватчик, который, в свою очередь, активирует модуль runbook. Этот модуль и изменяет размер масштабируемого набора. Вертикальное масштабирование можно настроить так:

1. Создайте учетную запись службы автоматизации Azure с возможностью запуска от имени.
2. Импортируйте в подписку модули runbook службы автоматизации Azure для масштабируемого набора виртуальных машин.
3. Добавьте в модуль Runbook веб-перехватчик.
4. Добавьте в свой масштабируемый набор виртуальных машин правило оповещения с использованием системы уведомлений веб-перехватчика.

> [!NOTE]
> Вертикальное автомасштабирование поддерживается только для виртуальных машин определенных размеров. Прежде чем принимать решение о переходе с одного размера на другой, сравните спецификации каждого размера (большее число не всегда свидетельствует о большем размере виртуальной машины). Вы можете выбрать для масштабирования следующие пары размеров:
> 
> | Пары размеров виртуальных машин, для которых можно применять вертикальное масштабирование |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Создание учетной записи службы автоматизации Azure с возможностью запуска от имени
Сначала необходимо создать учетную запись службы автоматизации Azure, в которой будут размещаться модули runbook, используемые в масштабируемых наборах виртуальных машин. Недавно в [службе автоматизации Azure](https://azure.microsoft.com/services/automation/) появился компонент "Учетная запись запуска от имени". Этот компонент позволяет настраивать субъект-службу для автоматического запуска модулей runbook от имени пользователя. Дополнительные сведения можно найти в разделе 

* [Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Импорт в подписку модулей Runbook службы автоматизации Azure для вертикального масштабирования
Модули runbook, необходимые для вертикального масштабирования масштабируемых наборов виртуальных машин, уже опубликованы в коллекции runbook службы автоматизации Azure. Вот как импортировать их в подписку:

* [Runbook и коллекции модулей для службы автоматизации Azure](../automation/automation-runbook-gallery.md)

Щелкните в меню модулей Runbook значок "Обзор коллекции".

![Модули Runbook, которые нужно импортировать][runbooks]

На рисунке ниже показаны модули Runbook, которые нужно импортировать. Выберите модуль Runbook в зависимости от того, какой вариант вертикального масштабирования вы хотите выполнить — с повторной подготовкой или без нее.

![Коллекция модулей Runbook][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Добавление веб-перехватчика в модуль Runbook.
После импорта модулей runbook в них необходимо добавить веб-перехватчик. Этого может потребовать система оповещений масштабируемого набора виртуальных машин. Дополнительные сведения о создании веб-перехватчика для модуля Runbook см. в этой статье:

* [Объекты Webhook в службе автоматизации Azure](../automation/automation-webhooks.md)

> [!NOTE]
> Прежде чем закрывать диалоговое окно, обязательно скопируйте универсальный код ресурса (URI) веб-перехватчика. Этот адрес понадобится в следующем разделе.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Добавление правила оповещения в масштабируемый набор виртуальных машин
Ниже приведен сценарий PowerShell, с помощью которого вы можете добавить в масштабируемый набор виртуальных машин правило оповещения. Дополнительные сведения о получении имени метрики для активации оповещения см. в статье [Общие метрики автомасштабирования Azure Insights](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Мы рекомендуем взвешенно подходить к определению временного окна для правила оповещения. Так вы сможете избежать слишком частой активации вертикального масштабирования, а также связанных с этой процедурой перерывов в работе службы. Начните с интервала минимум в 20–30 минут. Если перерывы в работе службы недопустимы, рассмотрите возможность горизонтального масштабирования.
> 
> 

Дополнительные сведения о создании правил оповещений см. в следующих статьях:

* [Примеры для быстрого запуска Azure Monitor с помощью PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Примеры команд для кроссплатформенного интерфейса командной строки 1.0 в Azure Monitor](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Сводка
В этой статье описан простой способ вертикального масштабирования. Указанные блоки (работа с учетной записью службы автоматизации, модулями Runbook, веб-перехватчиками и оповещениями) позволяют включить в настраиваемый набор действий самые разные события.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
