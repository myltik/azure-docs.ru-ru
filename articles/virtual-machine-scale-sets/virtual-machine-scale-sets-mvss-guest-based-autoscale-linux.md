---
title: Использование автоматического масштабирования Azure с метриками гостевой виртуальной машины в шаблоне масштабируемого набора Linux | Документация Майкрософт
description: Узнайте, как выполнять автоматическое масштабирование, используя метрики гостевой виртуальной машины в шаблоне масштабируемого набора виртуальных машин Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: negat
ms.openlocfilehash: 8e822d83dd3bafabfea60ad50224c87df226bdc6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781434"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Автоматическое масштабирование с помощью метрик гостевой виртуальной машины в шаблоне масштабируемого набора Linux

Имеется два типа метрик в Azure, которые собираются с виртуальных машин и масштабируемых наборов: некоторые собирают с виртуальной машины узла, а другие — с гостевой виртуальной машины. В целом, если используются стандартные показатели ЦП, диска и сети, метрики узла могут быть пригодными. Если же требуется больший набор показателей, тогда лучше использовать метрики гостевой виртуальной машины. Давайте рассмотрим различия между этими метриками.

Метрики узла проще и надежнее. Они не требуют дополнительной настройки, так как собираются с виртуальной машины узла. А для метрик гостевой виртуальной машины требуется установить [расширение системы диагностики Microsoft Azure](../virtual-machines/windows/extensions-diagnostics-template.md) или [расширение системы диагностики Azure для Linux](../virtual-machines/linux/diagnostic-extension.md) на гостевой виртуальной машине. Одна из основных причин использования метрик гостевой виртуальной машины вместо метрик узла заключается в том, что метрики гостевой виртуальной машины предоставляют больший набор показателей, чем метрики узла. Один из таких примеров — это метрики потребления памяти, которые доступны только через метрики гостевой виртуальной машины. Поддерживаемые метрики узла перечислены [здесь](../monitoring-and-diagnostics/monitoring-supported-metrics.md), а часто используемые метрики гостевой виртуальной машины — [здесь](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md). В этой статье показано, как изменить [минимальный приемлемый шаблон масштабируемого набора](./virtual-machine-scale-sets-mvss-start.md) для использования правил автоматического масштабирования на основе метрик гостевой виртуальной машины для масштабируемых наборов Linux.

## <a name="change-the-template-definition"></a>Изменение определения шаблона

Шаблон минимального приемлемого масштабируемого набора доступен [здесь](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), а шаблон для развертывания масштабируемого набора Linux с помощью автомасштабирования на основе гостевой системы доступен [здесь](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Давайте рассмотрим DIFF-файл, с помощью которого можно постепенно создать этот шаблон (`git diff minimum-viable-scale-set existing-vnet`).

Сначала добавьте параметры для `storageAccountName` и `storageAccountSasToken`. Агент диагностики хранит данные метрик в [таблице](../cosmos-db/table-storage-how-to-use-dotnet.md) в этой учетной записи хранения. Начиная с агента диагностики Linux версии 3.0 использование ключа доступа к хранилищу больше не поддерживается. Вместо этого используйте [маркер SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Затем измените масштабируемый набор `extensionProfile`, чтобы включить расширение диагностики. В этой конфигурации укажите идентификатор ресурса масштабируемого набора, который будет использоваться для сбора метрик, а также учетную запись хранения и маркер SAS для хранения метрик. Укажите то, как часто будут вычисляться метрики (в данном случае — каждую минуту) и какие метрики нужно отслеживать (в этом случае — это процент используемой памяти). Дополнительные сведения об этой конфигурации и других метриках см. в [этой документации](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Наконец, добавьте ресурс `autoscaleSettings`, чтобы настроить автоматическое масштабирование на основе этих метрик. Для ресурса указано предложение `dependsOn`, которое ссылается на масштабируемый набор, чтобы убедиться, что масштабируемый набор имеется, прежде чем пытаться его масштабировать. Если бы для автоматического масштабирования была выбрана другая метрика, вы бы использовали `counterSpecifier` из конфигурации расширения диагностики в качестве `metricName` в конфигурации автоматического масштабирования. Дополнительные сведения о настройке автомасштабирования см. в статье [Рекомендации по автомасштабированию](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md) и [Создание или обновление параметров автоматического масштабирования](https://msdn.microsoft.com/library/azure/dn931928.aspx).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
