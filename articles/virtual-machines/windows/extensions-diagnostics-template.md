---
title: "Добавление средств мониторинга и диагностики в виртуальную машину Azure | Документация Майкрософт"
description: "Использование шаблона диспетчера ресурсов Azure для создания новой виртуальной машины Windows с расширением диагностики Azure."
services: virtual-machines-windows
documentationcenter: 
author: sbtron
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e3ea1687e7fb6cc7af00e03b85fb48b0d7911275
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Использование мониторинга и системы диагностики с виртуальной машиной Windows и шаблонами Azure Resource Manager
Расширение системы диагностики Azure позволяет использовать возможности мониторинга и диагностики в виртуальной машине Azure под управлением Windows. Вы можете включить эти возможности на виртуальной машине, как часть шаблона диспетчера ресурсов Azure, включая расширение. Дополнительные сведения о включении любого расширения в шаблон виртуальной машины см. в статье [Создание шаблонов диспетчера ресурсов Azure с расширениями виртуальных машин](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions). В этой статье описывается, как добавить расширение системы диагностики Azure в шаблон виртуальной машины Windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Добавление расширения системы диагностики Azure в определения ресурсов виртуальной машины
Чтобы включить расширение диагностики на виртуальной машине Windows, необходимо добавить расширение виртуальной Машины в качестве ресурса шаблона диспетчера ресурсов.

Для простой виртуальной машины на основе диспетчера ресурсов добавьте конфигурацию расширения в массив *resources* для соответствующей виртуальной машины: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Другое соглашение о распространенных является добавление конфигурации расширения в корневом узле ресурсы из шаблона вместо того чтобы определять его в узле ресурсы виртуальной машины. Таким образом, необходимо явно указать иерархические отношения между расширения и виртуальной машины с *имя* и *тип* значения. Например:  

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Расширение всегда связано с виртуальной машиной. Его можно определить непосредственно в узле ресурсов виртуальной машины или на базовом уровне и связать его с виртуальной машиной с помощью соглашения об иерархическом именовании.

Для масштабируемых наборов виртуальных машин конфигурация расширений указывается в свойстве *extensionProfile* параметра *VirtualMachineProfile*.

Свойство *publisher* со значением **Microsoft.Azure.Diagnostics** и *type* со значением **IaaSDiagnostics** уникальным образом определяет расширение системы диагностики Azure.

Значение свойства *name* можно использовать для обращения к расширению в группе ресурсов. Значения этого свойства конкретно к **Microsoft.Insights.VMDiagnosticsSettings** позволяет легко определить с помощью портала Azure, гарантируя, что мониторинг диаграммы Показать копирование правильно на портале Azure.

Значение *typeHandlerVersion* указывает версию расширения, которую нужно использовать. Установка *autoUpgradeMinorVersion* дополнительный номер версии для **true** гарантирует, что вы получаете последняя вспомогательная версия расширения, доступные. Рекомендуется всегда указывать для параметра *autoUpgradeMinorVersion* значение **true** , чтобы иметь возможность использовать последнее доступное расширение системы диагностики со всеми новыми функциями и исправлениями ошибок. 

Элемент *settings* содержит свойства конфигураций для расширения, которые можно задать и считывать с расширения (иногда они называются открытой конфигурацией). *Xmlcfg* свойство содержит конфигурацию на основе xml для журналов диагностики, счетчики производительности и т. д, собранных агентом диагностики. Дополнительные сведения о схеме XML см. в разделе [Схема конфигурации системы диагностики](https://msdn.microsoft.com/library/azure/dn782207.aspx). Традиционно конфигурации в формате XML хранятся в качестве переменных в шаблоне диспетчера ресурсов Azure, после чего они объединяются и шифруются в кодировке base64, чтобы задать значение для *xmlcfg*. Дополнительные сведения о способах хранения конфигурации в формате XML в качестве переменных см. в разделе [Переменные конфигурации диагностики](#diagnostics-configuration-variables). *StorageAccount* свойство задает имя учетной записи хранилища для диагностики, какие данные передаются. 

Свойства в параметре *protectedSettings* (иногда называются закрытой конфигурацией) можно задать, но затем невозможно считать. Только для записи характер *protectedSettings* позволяет использовать его для хранения секретов как ключ учетной записи хранения которую записываются данные диагностики.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Указание учетной записи хранения диагностических данных в качестве параметра
Диагностика расширения json выше фрагменте предполагается два параметра *existingdiagnosticsStorageAccountName* и *existingdiagnosticsStorageResourceGroup* для задания хранилища данных диагностики Учетная запись, где хранятся данные диагностики. Указание учетной записи хранения диагностики, как параметр позволяет легко изменить учетную запись хранилища диагностики в различных средах, например вы можете использовать учетную запись хранения различных диагностики для тестирования, а другой для вашей развертывание в рабочей среде.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }        
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
      }
}
```

Рекомендуется указать учетную запись хранения диагностических данных в группе ресурсов, отличной от группы ресурсов для виртуальной машины. Группа ресурсов может считаться единицей развертывания с отдельным временем существования, а виртуальную машину можно развернуть, а затем повторно развернуть после соответствующих обновлений конфигураций. Но диагностические данные можно продолжить хранить в одной учетной записи в этих развертываниях виртуальных машин. Если разместить учетную запись хранения в другом ресурсе, она может получать данные из различных развертываний виртуальных машин, что упрощает устранение неполадок в разных версиях.

> [!NOTE]
> При создании шаблона виртуальной машины windows из Visual Studio, учетной записи хранения по умолчанию может быть присвоено использовать одну и ту же учетную запись хранилища, где загружается виртуальный жесткий ДИСК виртуальной машины. Это позволяет упростить начальную настройку виртуальной машины. Повторно выделить шаблона, чтобы использовать другую учетную запись хранения, можно передать в качестве параметра. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Переменные конфигурации диагностики
Во фрагменте кода JSON расширения системы диагностики выше определена переменная *accountid* , позволяющая упростить получение ключа учетной записи хранения для хранилища диагностических данных:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

Свойство *xmlcfg* расширения системы диагностики определено путем использования нескольких объединенных переменных. Значения этих переменных находятся в формате XML, поэтому при настройке переменных JSON их необходимо должным образом экранировать.

Во фрагменте кода ниже приведен XML-файл конфигурации диагностики, который собирает данные стандартных системных счетчиков производительности и некоторые журналы событий и инфраструктуры диагностики Windows. Он экранирован должным образом и написан в правильном формате, поэтому конфигурацию можно вставлять непосредственно в раздел переменных шаблона. Дополнительные примеры XML-файлов конфигурации в формате, удобном для чтения, см. в разделе [Схема конфигурации системы диагностики](https://msdn.microsoft.com/library/azure/dn782207.aspx).

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Узел xml определение метрики в представленной выше конфигурации — это элемент важные настройки, как он определяет, как счетчики производительности, определенные ранее в XML-данные в *PerformanceCounter* узел, статистически обрабатываются и сохраняются. 

> [!IMPORTANT]
> Эти метрики обеспечивают данными диаграммы мониторинга и оповещения на портале Azure.  Узел **Metrics** с *resourceID* и **MetricAggregation** должен быть включен в конфигурацию диагностики виртуальной машины, если вы хотите видеть на портале Azure данные мониторинга этой виртуальной машины. 
> 
> 

Ниже приведен пример XML-кода для определений метрик: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

Свойство *resourceID* уникальным образом определяет виртуальную машину в подписке. Обязательно используйте функции subscription() и resourceGroup(), чтобы шаблон автоматически обновлял эти значения на основе подписки и группы ресурсов, в которых выполняется развертывание.

При создании нескольких виртуальных машин в цикле, необходимо заполнить *resourceID* значение с функцией copyIndex() чтобы правильно различать каждого отдельных виртуальных Машин. Для поддержки такого сценария свойство *xmlCfg* можно обновить следующим образом.  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Значение MetricAggregation *PT1H* и *PT1M* обозначает значение агрегирования за минуту и час.

## <a name="wadmetrics-tables-in-storage"></a>Таблицы WADMetrics в хранилище
Приведенной выше конфигурации метрики приводит к возникновению ошибки таблиц в учетной записи хранения диагностики с следующие соглашения об именовании:

* **WADMetrics**: стандартного префикса для всех таблиц WADMetrics
* **PT1H** или **PT1M**: означает, что таблица содержит сводные данные более 1 часа или 1 минута
* **P10D**: обозначает таблица будет содержать данные для 10 дней после начала сбора данных таблицы
* **V2S**: строковая константа
* **ГГГГММДД**: дата таблице начала сбор данных

Например, таблица *WADMetricsPT1HP10DV2S20151108* будет содержать данные метрик, агрегированные за час в течение 10 дней, начиная с 11 ноября 2015 года.    

Каждая таблица WADMetrics будет содержать следующие столбцы:

* **PartitionKey**: ключ раздела создается на основе *resourceID* значение для уникальной идентификации ресурса виртуальной Машины. Например: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey**: формат `<Descending time tick>:<Performance Counter Name>`. Такты времени вычисляются в порядке убывания следующим образом: от максимального количества тактов времени отнимается время начала периода агрегирования. Например, если период выборки, начатый 10 ноября 2015 г. и будет 00:00Hrs UTC, а затем вычисления: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Для доступно байт памяти счетчиков производительности ключ строки будет выглядеть как:`2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: имя счетчика производительности. Это значение соответствует *counterSpecifier* , определенному в XML-файле конфигурации.
* **Максимальная**: максимальное значение счетчика производительности в течение периода статистической обработки.
* **Минимальное**: минимальное значение счетчика производительности в течение периода статистической обработки.
* **Общее**: сообщил сумму всех значений счетчика производительности в течение периода статистической обработки.
* **Число**: общее количество значений в отчеты для счетчика производительности.
* **Среднее**: среднее (общий или count) значение счетчика производительности в течение периода статистической обработки.

## <a name="next-steps"></a>Следующие шаги
* Полный пример шаблона виртуальной машины Windows с расширением диагностики, в разделе [201-vm--диагностики расширение мониторинга](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Развертывание шаблона диспетчера ресурсов Azure с помощью [Azure PowerShell](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) или [командной строки Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Узнайте больше о [создании шаблонов диспетчера ресурсов Azure](../../resource-group-authoring-templates.md)
