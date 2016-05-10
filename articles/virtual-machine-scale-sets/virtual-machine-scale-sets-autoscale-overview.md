<properties
	pageTitle="Автоматическое масштабирование и масштабируемые наборы виртуальных машин | Microsoft Azure"
	description="Узнайте о том, как использовать диагностику и ресурсы автоматического масштабирования для автоматического масштабирования виртуальных машин в масштабируемом наборе."
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Автоматическое масштабирование и масштабируемые наборы виртуальных машин

Автоматическое масштабирование виртуальных машин в масштабируемом наборе — это процесс создания или удаления входящих в набор компьютеров в соответствии с потребностями приложений, а также для соблюдения требований к производительности и соглашений об уровне обслуживания. По мере роста рабочей нагрузки приложению могут требоваться дополнительные ресурсы для эффективного выполнения задач.

Автоматическое масштабирование — это автоматизированный процесс, который помогает упростить управление ресурсами. Вы можете обойтись без постоянного мониторинга производительности системы, а также необходимости принимать решения о добавлении или удалении ресурсов. Масштабирование обеспечивает эластичность системы. При увеличении нагрузки могут быть подготовлены дополнительные ресурсы, а при снижении потребности в них такие ресурсы освобождаются. Таким образом снижаются затраты, но сохраняется достаточная производительность; кроме того, выполняются соглашения об уровне обслуживания.

Настроить автоматическое масштабирование набора ресурсов можно с помощью шаблона Azure Resource Manager, с помощью Azure PowerShell или интерфейса командной строки Azure.

## Настройка масштабирования с помощью шаблонов Resource Manager

Чтобы не развертывать (с последующим управлением) каждый ресурс для приложения по отдельности, вы можете создать шаблон Azure Resource Manager, который развертывает и подготавливает все нужные ресурсы в ходе одной скоординированной операции. В шаблоне определяются ресурсы приложения и указываются параметры развертывания для разных сред. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания. Узнайте больше о [создании шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

В шаблоне необходимо указать элемент capacity.

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

Элемент capacity определяет количество виртуальных машин в наборе. Вы можете вручную изменить значение этого параметра, развернув шаблон с новым значением. Если вы развертываете шаблон только для изменения параметра capacity, достаточно включить в такой шаблон только элемент SKU с новым значением capacity.

Чтобы параметр capacity изменялся для вашего набора автоматически, примените ресурс autoscaleSettings, предоставляемый Azure Resource Manager, и установите расширение системы диагностики Azure на компьютерах в наборе.

### Настройка расширения системы диагностики Azure

Автоматическое масштабирование выполняется, только если для каждой виртуальной машины из масштабируемого набора собираются метрики. Расширение системы диагностики Azure предусматривает возможности для мониторинга и диагностики, необходимые для автоматического масштабирования ресурсов. Расширение можно установить в составе шаблона Resource Manager. Дополнительную информацию о расширении системы диагностики см. в статье [Создание виртуальной машины Windows с мониторингом и диагностикой с использованием шаблона диспетчера ресурсов Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

В следующем примере показано использование переменных в шаблоне для настройки расширения диагностики.

	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\Thread Count" sampleRate="PT15S" unit="Percent"><annotation displayName="Thread Count" locale="ru-RU"/></PerformanceCounterConfiguration>",
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

![Снимок экрана: счетчик производительности Windows Server до повышения нагрузки](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

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
- **value** — это количество виртуальных машин, которое нужно добавить в масштабируемый набор или удалить оттуда. Для этого параметра должно быть указано значение не меньше 1.
- **cooldown** — это общее время ожидания с момента последнего действия масштабирования перед выполнением следующего действия. Допускаются значения в диапазоне от одной минуты до одной недели.

В зависимости от выбранного счетчика производительности некоторые элементы в шаблоне конфигурации используются иначе. В приведенном примере в качестве счетчика производительности используется счетчик потоков. Установлено пороговое значение 650 для увеличения масштаба и 550 для его уменьшения. Если вы будете использовать такие счетчики, как %Processor Time (процент загруженности процессора), пороговое значение для действия масштабирования следует указывать в процентах.

Когда нагрузка на виртуальные машины в наборе достигает уровня, установленного для действия масштабирования, количество компьютеров в наборе изменяется в соответствии с параметром value, заданным в шаблоне. Рассмотрим пример, в котором для масштабируемого набора параметр capacity имеет значение 3, а параметр value для действия имеет значение 1.

![Снимок экрана: количество машин в наборе до автоматического масштабирования](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Предположим, в этом наборе создается нагрузка, приводящая к повышению среднего количества потоков выше порогового значения 650.

![Снимок экрана: счетчик производительности Windows Server после повышения нагрузки](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

В этой ситуации запускается увеличение масштаба, при котором размер набора (параметр capacity) увеличивается на единицу.

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

И в масштабируемый набор добавляется новая виртуальная машина.

![Снимок экрана: количество машин в наборе после автоматического масштабирования](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Если по истечении периода ожидания (параметр cooldown) среднее количество потоков остается выше 600, в набор добавляется еще один компьютер. Если среднее число потоков окажется ниже 550, размер масштабируемого набора уменьшается на единицу и один компьютер удаляется из набора.

## Изучение действий масштабирования

- [Портал Azure](https://portal.azure.com/) — сейчас с помощью портала время можно получить ограниченный объем сведений.
- [Обозреватель ресурсов Azure](https://resources.azure.com/) — это лучшее средство для просмотра текущего состояния масштабируемого набора.
- [Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/) — предоставляет несколько командлетов для получения сведений о масштабируемых наборах, например **Get-AzureRmResource** или **Get-Autoscalesetting**.
- [Azure CLI](../xplat-cli-azure-resource-manager.md) — используйте команду **azure resource show**, чтобы получить сведения о наборе.
- Подключитесь к основной виртуальной машине так же, как к любому другому компьютеру. Теперь вы сможете удаленно подключаться к виртуальным машинам в масштабируемом наборе, чтобы отслеживать отдельные процессы.

## Дальнейшие действия

1. Дополнительные сведения о создании первого масштабируемого набора см. в статье [Create a Windows Virtual Machine Scale Set](virtual-machine-scale-sets-windows-create.md) (Создание масштабируемого набора с виртуальными машинами Windows).
2. Также в статьях [Autoscale Windows Virtual Machine Scale Sets](virtual-machine-scale-sets-windows-autoscale.md) (Автоматическое масштабирование масштабируемых наборов с виртуальными машинами Windows) или [Autoscale Linux Virtual Machine Scale Sets](virtual-machine-scale-sets-linux-autoscale.md) (Автоматическое масштабирование масштабируемых наборов с виртуальными машинами Linux) вы найдете примеры, иллюстрирующие создание масштабируемых наборов с автоматическим масштабированием.

<!---HONumber=AcomDC_0427_2016-->