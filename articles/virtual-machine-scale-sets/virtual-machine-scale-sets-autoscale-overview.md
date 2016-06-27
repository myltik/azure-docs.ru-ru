<properties
	pageTitle="Автоматическое масштабирование и наборы масштабирования виртуальных машин | Microsoft Azure"
	description="Узнайте о том, как использовать диагностику и ресурсы автоматического масштабирования для автоматического масштабирования виртуальных машин в наборе масштабирования."
    services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="davidmu"/>

# Автоматическое масштабирование и наборы масштабирования виртуальных машин

Автоматическое масштабирование виртуальных машин в масштабируемом наборе — это процесс создания или удаления входящих в набор компьютеров в соответствии с потребностями приложений, а также для соблюдения требований к производительности и соглашений об уровне обслуживания. По мере роста рабочей нагрузки для эффективного выполнения задач приложению могут потребоваться дополнительные ресурсы.

Автоматическое масштабирование — это автоматизированный процесс, который помогает упростить управление ресурсами. Вы можете обойтись без постоянного мониторинга производительности системы, а также необходимости принимать решения о добавлении или удалении ресурсов. Масштабирование обеспечивает эластичность системы. При увеличении нагрузки могут быть задействованы дополнительные ресурсы, а при снижении потребности в них такие ресурсы освобождаются. Таким образом можно достичь сокращения затрат, сохранив достаточную производительность без нарушения условий соглашений об уровне обслуживания.

Настроить автоматическое масштабирование набора ресурсов можно с помощью шаблона Azure Resource Manager, с помощью Azure PowerShell или интерфейса командной строки Azure.

## Настройка масштабирования с помощью шаблонов Resource Manager

Чтобы не развертывать (с последующим управлением) каждый ресурс для приложения по отдельности, вы можете создать шаблон Azure Resource Manager, который развертывает и подготавливает все нужные ресурсы в ходе одной скоординированной операции. В шаблоне определяются ресурсы приложения и указываются параметры развертывания для разных сред. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания. Узнайте больше о [создании шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

В шаблоне необходимо указать элемент capacity.

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

Элемент capacity определяет количество виртуальных машин в наборе. Вы можете вручную изменить значение этого параметра, развернув шаблон с новым значением. Если вы развертываете шаблон только для изменения параметра capacity, то достаточно включить в такой шаблон только элемент SKU с новым значением capacity.

Чтобы параметр capacity изменялся для вашего набора автоматически, примените ресурс autoscaleSettings, предоставляемый Azure Resource Manager, и установите расширение системы диагностики Azure на компьютерах в наборе.

### Настройка расширения системы диагностики Azure

Автоматическое масштабирование выполняется, только если для каждой виртуальной машины из масштабируемого набора собираются метрики. Расширение системы диагностики Azure предусматривает возможности для мониторинга и диагностики, необходимые для автоматического масштабирования ресурсов. Расширение можно установить в составе шаблона Resource Manager.

В следующем примере показано использование переменных в шаблоне для настройки расширения диагностики.

	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\Thread Count" sampleRate="PT15S" unit="Percent"><annotation displayName="Thread Count" locale="ru-RU"/></PerformanceCounterConfiguration></PerformanceCounters>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

При развертывании шаблона предоставляются значения для некоторых параметров, таких как имя учетной записи хранилища, где хранятся данные, и имя масштабируемого набора, из которого нужно собирать данные. В этом примере для Windows Server собираются только счетчики производительности потоков, но использовать для сбора диагностической информации и включать в настройки расширения можно любые счетчики производительности, доступные в среде Windows или Linux.

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

Когда выполняется расширение диагностики, данные собираются в таблице, которая расположена в указанной учетной записи хранения. Собранные данные можно найти в таблице WADPerformanceCounters. Например, так вы получите данные о количестве потоков, собранные при первоначальном создании виртуальных машин в масштабируемом наборе.

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### Настройка ресурса autoScaleSettings

Ресурс AutoscaleSettings использует данные, собранные расширением системы диагностики, для принятия решений об увеличении или уменьшении количестве виртуальных машин в масштабируемом наборе.

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

- **metricName** — здесь должно быть имя того счетчика производительности, который вы указали в переменной wadperfcounter. В нашем примере это счетчик потоков (Thread Count).  
- **metricResourceUri** — это идентификатор ресурса масштабируемого набора виртуальных машин. Этот идентификатор содержит имя группы ресурсов, имя поставщика ресурсов и имя набора, который мы будем масштабировать.
- **timeGrain** — это степень детализации собираемых метрик. В приведенном выше примере данные собираются с интервалами в одну минуту. Это значение используется в сочетании со значением timeWindow.
- **statistic** — этот параметр определяет, как объединяются метрики для выполнения автоматического масштабирования. Возможные значения: Average (Среднее), Min (Минимальное), Max (Максимальное).
- **timeWindow** — это интервал, во время которого собираются данные об экземплярах. Значение должно составлять от 5 минут до 12 часов.
- **timeAggregation** — этот параметр определяет способ объединения собранных данных с течением времени. Значение по умолчанию — Average (Среднее). Возможные значения: Average (Среднее), Minimum (Минимальное), Maximum (Максимальное), Last (Последнее), Total (Всего), Count (Количество).
- **operator** — это оператор, который используется для сравнения данных метрики и порогового значения. Возможные значения: Equals (Равно), NotEquals (Не равно), GreaterThan (Больше), GreaterThanOrEqual (Больше или равно), LessThan (Меньше), LessThanOrEqual (Меньше или равно).
- **threshold** — это значение, которое активирует действие масштабирования. Между пороговыми значениями, которые вы укажете для действий по увеличению и уменьшению масштаба, должна быть достаточно большая разница. Если эти значения будут совпадать (например, если указать 600 потоков для обоих параметров в приведенном выше примере), система будет постоянно ожидать как увеличения, так и уменьшения размера набора. В итоге действия масштабирования выполняться не будут.
- **direction** — этот параметр определяет действие, которое выполняется, когда достигается пороговое значение. Допустимые значения: Increase (Увеличить) или Decrease (Уменьшить).
- **type** — это тип действия, которое должно выполняться. Для этого параметра должно быть установлено значение ChangeCount (Изменить количество).
- **value** — это количество виртуальных машин, которое нужно добавить в набор масштабирования или удалить оттуда. Для этого параметра должно быть указано значение не меньше 1.
- **cooldown** — это общее время ожидания с момента последнего действия масштабирования перед выполнением следующего действия. Допускаются значения в диапазоне от одной минуты до одной недели.

В зависимости от выбранного счетчика производительности некоторые элементы в шаблоне конфигурации используются иначе. В приведенном примере в качестве счетчика производительности используется счетчик потоков. Установлено пороговое значение 650 для увеличения масштаба и 550 для его уменьшения. Если вы будете использовать такие счетчики, как %Processor Time (процент загруженности процессора), пороговое значение для действия масштабирования следует указывать в процентах.

Когда нагрузка на виртуальные машины в наборе достигает уровня, установленного для действия масштабирования, количество компьютеров в наборе изменяется в соответствии с параметром value, заданным в шаблоне. Рассмотрим пример, в котором для масштабируемого набора параметр capacity имеет значение 3, а параметр value для действия имеет значение 1.

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Предположим, в этом наборе создается нагрузка, приводящая к повышению среднего количества потоков выше порогового значения 650.

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

В этой ситуации запускается увеличение масштаба, при котором размер набора (параметр capacity) увеличивается на единицу.

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

И в масштабируемый набор добавляется новая виртуальная машина.

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Если по истечении периода ожидания (параметр cooldown) среднее количество потоков остается выше 600, в набор добавляется еще один компьютер. Если среднее число потоков окажется ниже 550, размер масштабируемого набора уменьшается на единицу и один компьютер удаляется из набора.

## Настройка масштабирования с помощью Azure PowerShell

Azure PowerShell можно использовать для настройки автоматического масштабирования набора масштабирования. Ниже приведен пример использования PowerShell для создания правил, которые были описаны ранее в этой статье.

    $rule1 = New-AutoscaleRule -MetricName "\Processor(_Total)\Thread Count" -MetricResourceId "/subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1" -Operator GreaterThan -MetricStatistic Average -Threshold 650 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"  
 
    $rule2 = New-AutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/rainvmss/providers/Microsoft.Compute/virtualMachineScaleSets/rainvmss1" -Operator LessThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue "2" 
 
    $profile1 = New-AutoscaleProfile -DefaultCapacity "2" -MaximumCapacity "10" -MinimumCapacity "2" -Rules $rule1, $rule2 -Name "ScalePuppy" 
 
    Add-AutoscaleSetting -Location "East US" -Name 'MyScaleVMSSSetting' -ResourceGroup rainvmss -TargetResourceId "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/rainvmss/providers/Microsoft.Compute/virtualMachineScaleSets/rainvmss1" -AutoscaleProfiles $profile1 

## Настройка масштабирования с помощью интерфейса командной строки Azure (Azure CLI)

(Информация еще отсутствует)

## Изучение действий масштабирования

- [Портал Azure]() — сейчас с помощью портала время можно получить ограниченный объем сведений.
- [Обозреватель ресурсов Azure]() — это лучший инструмент для просмотра текущего состояния масштабируемого набора. Перейдите по этому пути, чтобы открыть представление экземпляров созданного набора масштабирования: subscriptions > {ваша подписка} > resourceGroups > {ваша группа ресурсов} > providers > Microsoft.Compute > virtualMachineScaleSets > {ваш набор масштабирования} > virtualMachines.
- Azure PowerShell — используйте эту команду, чтобы получить сведения:

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
        
- Подключитесь к основной виртуальной машине так же, как к любому другому компьютеру. Теперь вы сможете удаленно подключаться к виртуальным машинам в масштабируемом наборе, чтобы отслеживать отдельные процессы.

## Дальнейшие действия

- Ознакомьтесь с примером того, как можно создать набор с настроенным автоматическим масштабированием, в разделе [Автоматическое масштабирование машин в масштабируемом наборе виртуальных машин](virtual-machine-scale-sets-windows-autoscale.md).
- Просмотрите примеры функций мониторинга Azure Insights в разделе [Примеры для быстрого запуска Azure Insights с помощью PowerShell](../azure-portal/insights-powershell-samples.md).
- Узнайте о возможностях уведомления в разделах [Использование действий автомасштабирования для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Insights](../azure-portal/insights-autoscale-to-webhook-email.md) и [Использование журналов аудита для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Insights](../azure-portal/insights-auditlog-to-webhook-email.md).

<!---HONumber=AcomDC_0615_2016-->