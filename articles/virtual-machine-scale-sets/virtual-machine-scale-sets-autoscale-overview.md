---
title: "Автомасштабирование и масштабируемые наборы виртуальных машин | Документация Майкрософт"
description: "Узнайте о том, как использовать диагностику и ресурсы автоматического масштабирования для автоматического масштабирования виртуальных машин в наборе масштабирования."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 090374b057a62251e40ccc41f60f61e84e08a03f
ms.openlocfilehash: ff137ead5e3490a129b36c959040d3571bff7669


---
# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>Автоматическое масштабирование и наборы масштабирования виртуальных машин
Автоматическое масштабирование виртуальных машин (ВМ) в наборе масштабирования — это процесс создания или удаления входящих в набор ВМ в соответствии требованиями к производительности. По мере роста рабочей нагрузки для эффективного выполнения задач приложению могут потребоваться дополнительные ресурсы.

Автоматическое масштабирование — это автоматизированный процесс, который помогает упростить управление ресурсами. Вы сможете снизить издержки, и при этом не вам не нужно постоянно отслеживать производительность системы или решать, как управлять ресурсами. Масштабирование — это эластичный процесс. При увеличении нагрузки могут добавляться дополнительные ресурсы, а при уменьшении запросов ресурсы можно удалять, что позволяет снизить затраты при сохранении требуемой производительности.

Настроить автоматическое масштабирование набора ресурсов можно с помощью шаблона Azure Resource Manager, с помощью Azure PowerShell, интерфейса командной строки Azure или портала Azure.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Настройка масштабирования с помощью шаблонов Resource Manager
Чтобы не развертывать (с последующим управлением) каждый ресурс для приложения по отдельности, вы можете создать шаблон Azure Resource Manager, который развертывает все нужные ресурсы в ходе одной скоординированной операции. В шаблоне определяются ресурсы приложения и указываются параметры развертывания для разных сред. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания. Узнайте больше о [создании шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

В шаблоне необходимо указать элемент capacity.

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

Элемент capacity определяет количество виртуальных машин в наборе. Вы можете вручную изменить значение этого параметра, развернув шаблон с новым значением. Если вы развертываете шаблон только для изменения емкости набора, вам достаточно включить в шаблон только элемент SKU с новым значением capacity.

Вы можете автоматически изменять емкость набора масштабирования, используя ресурс autoscaleSettings и расширение системы диагностики.

### <a name="configure-the-azure-diagnostics-extension"></a>Настройка расширения системы диагностики Azure
Автоматическое масштабирование выполняется, только если для каждой виртуальной машины из масштабируемого набора собираются метрики. Расширение системы диагностики Azure предусматривает возможности для мониторинга и диагностики, необходимые для автоматического масштабирования ресурсов. Расширение можно установить в составе шаблона Resource Manager.

В следующем примере показано использование переменных в шаблоне для настройки расширения диагностики.

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Параметры предоставляются при развертывании шаблона. В нашем примере это имя учетной записи хранения, в которой будут сохраняться данные, и имя набора масштабирования, из которого нужно собирать данные. В этом примере для Windows Server собираются только данные счетчика производительности (счетчика потоков). Для сбора диагностической информации можно включать в настройки расширения любые счетчики производительности, доступные в среде Windows или Linux.

В этом примере показано определение расширения в шаблоне.

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Когда выполняется расширение диагностики, данные собираются в таблице, которая расположена в указанной учетной записи хранения. Собранные данные помещаются в таблицу WADPerformanceCounters.

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Настройка ресурса autoScaleSettings
Ресурс AutoscaleSettings использует данные, собранные расширением системы диагностики, для принятия решений об увеличении или уменьшении числа ВМ в наборе масштабирования.

В этом примере показана конфигурация ресурса в шаблоне.

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

В приведенном выше примере создаются два правила, определяющие действия автоматического масштабирования. Первое правило определяет действие по увеличению масштаба, а второе — по его уменьшению. Для этих правил указаны значения следующих параметров:

* **metricName** — здесь должно быть имя счетчика производительности, который вы указали в переменной wadperfcounter для расширения диагностики. В нашем примере это счетчик потоков (Thread Count).  
* **metricResourceUri**. В этом параметре указывается идентификатор ресурса набора масштабирования виртуальных машин. Этот идентификатор содержит имя группы ресурсов, имя поставщика ресурсов и имя набора, который мы будем масштабировать.
* **timeGrain**. В этом параметре указывается степень детализации собираемых метрик. В приведенном выше примере данные собираются с интервалами в одну минуту. Это значение используется вместе со значением timeWindow.
* **statistic**. Этот параметр определяет, как объединяются метрики для автоматического масштабирования. Возможные значения: Average (Среднее), Min (Минимальное), Max (Максимальное).
* **timeWindow**. В этом параметре указывается интервал, в пределах которого собираются данные об экземплярах. Значение должно составлять от 5 минут до 12 часов.
* **timeAggregation**. Этот параметр определяет способ объединения собранных данных с течением времени. Значение по умолчанию — Average (Среднее). Возможные значения: Average (Среднее), Minimum (Минимальное), Maximum (Максимальное), Last (Последнее), Total (Всего), Count (Количество).
* **operator**. Оператор, который используется для сравнения данных метрики с пороговым значением. Возможные значения: Equals (Равно), NotEquals (Не равно), GreaterThan (Больше), GreaterThanOrEqual (Больше или равно), LessThan (Меньше), LessThanOrEqual (Меньше или равно).
* **threshold**. Значение этого параметра активирует действие масштабирования. Между пороговыми значениями, которые вы укажете для действий по увеличению и уменьшению масштаба, должна быть достаточно большая разница. Если вы укажете для них одинаковые значения, система будет постоянно ожидать изменений. В итоге действия масштабирования выполнены не будут. Например, если в предыдущем примере для двух параметров указать значение 600, масштабирование не будет выполнено.
* **direction**. Этот параметр определяет действие, которое запускается при достижении порогового значения. Допустимые значения: Increase (Увеличить) или Decrease (Уменьшить).
* **type**. В этом параметре указывается тип действия, которое должно выполняться. Для этого параметра должно быть установлено значение ChangeCount (Изменить количество).
* **value** — это число ВМ, которые добавляются в набор масштабирования или удаляются из него. Для этого параметра должно быть указано значение не меньше 1.
* **cooldown**. В этом параметре указывается время ожидания с момента выполнения последнего действия масштабирования до начала следующего. Допустимые значения: от одной минуты до одной недели.

В зависимости от выбранного счетчика производительности некоторые элементы в шаблоне конфигурации используются иначе. В приведенном примере в качестве счетчика производительности используется счетчик потоков. Установлено пороговое значение 650 для увеличения масштаба и 550 для уменьшения масштаба. Если вы будете использовать такие счетчики, как загруженность процессора в процентах, пороговое значение для действия масштабирования следует указать в процентах.

Когда нагрузка на ВМ достигает уровня, установленного для действия масштабирования, емкость набора изменяется в соответствии с параметром value, заданным в шаблоне. Рассмотрим пример, в котором для масштабируемого набора параметр capacity имеет значение 3, а параметр value для действия имеет значение 1.

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Предположим, в этом наборе создается нагрузка, приводящая к повышению среднего количества потоков выше порогового значения 650.

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

В этом примере масштаб увеличивается: емкость набора (число ВМ) увеличивается на единицу.

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

И в масштабируемый набор добавляется новая виртуальная машина.

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Если по истечении периода ожидания (параметр cooldown) среднее количество потоков остается выше 600, в набор добавляется еще один компьютер. Если среднее число потоков окажется ниже 550, размер масштабируемого набора уменьшается на единицу и один компьютер удаляется из набора.

## <a name="set-up-scaling-using-azure-powershell"></a>Настройка масштабирования с помощью Azure PowerShell
Примеры использования PowerShell для настройки автоматического масштабирования см. в статье [Примеры для быстрого запуска Azure Insights с помощью PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="set-up-scaling-using-azure-cli"></a>Настройка масштабирования с помощью интерфейса командной строки Azure (Azure CLI)
Примеры использования интерфейса командной строки Azure для настройки автоматического масштабирования см. в статье [Примеры команд для межплатформенного интерфейса командной строки Azure Insights](../monitoring-and-diagnostics/insights-cli-samples.md).

## <a name="set-up-scaling-using-the-azure-portal"></a>Настройка масштабирования с помощью портала Azure
Примеры использования портала Azure для настройки автоматического масштабирования, см. в статье [Создание набора масштабирования с помощью портала Azure](virtual-machine-scale-sets-portal-create.md).

## <a name="investigate-scaling-actions"></a>Изучение действий масштабирования
* Портал Azure. Сейчас с помощью портала можно получить ограниченный объем сведений.
* Обозреватель ресурсов Azure. Это лучший инструмент для просмотра текущего состояния набора масштабирования. Перейдите по этому пути, чтобы открыть представление экземпляров созданного масштабируемого набора: subscriptions > {ваша подписка} > resourceGroups > {ваша группа ресурсов} > providers > Microsoft.Compute > virtualMachineScaleSets > {ваш масштабируемый набор} > virtualMachines.
* Azure PowerShell — используйте эту команду, чтобы получить сведения:
  
        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
* Подключитесь к основной виртуальной машине так же, как к любому другому компьютеру. Теперь вы сможете удаленно подключаться к виртуальным машинам в масштабируемом наборе, чтобы отслеживать отдельные процессы.

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с примером того, как можно создать набор с настроенным автоматическим масштабированием, в статье [Автоматическое масштабирование машин в масштабируемом наборе виртуальных машин](virtual-machine-scale-sets-windows-autoscale.md) .
* Просмотрите примеры функций мониторинга Azure Monitor в статье [Примеры для быстрого запуска Azure Insights с помощью PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md).
* Узнайте о возможностях уведомлений в статье [Использование действий автомасштабирования для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Insights](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
* Узнайте, как [использовать журналы аудита для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Insights](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
* Узнайте больше о [расширенных сценариях автомасштабирования](virtual-machine-scale-sets-advanced-autoscale.md).




<!--HONumber=Feb17_HO2-->


