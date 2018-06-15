---
title: Автомасштабирование масштабируемых наборов виртуальных машин с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как создать правила автомасштабирования для масштабируемых наборов виртуальных машин с помощью Azure CLI 2.0.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8552f6b2723fef2c61d49a34d2d60c2a6c209a32
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201345"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Автоматическое масштабирование масштабируемых наборов виртуальных машин с помощью Azure CLI 2.0
При создании масштабируемого набора вы определяете количество экземпляров виртуальных машин для запуска. По мере изменения потребностей приложения можно автоматически увеличивать или уменьшать это количество. Возможность автоматического масштабирования позволяет удовлетворить пользовательский спрос или среагировать на изменения производительности приложения на протяжении его жизненного цикла.

В этой статье описывается, как создать правила автомасштабирования с помощью Azure CLI 2.0, которые отслеживают производительность экземпляров виртуальных машин в масштабируемом наборе. С помощью этих правил можно увеличить или уменьшить количество экземпляров виртуальных машин в ответ на метрики производительности. Эти действия также можно выполнить с помощью [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) или [портала Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>предварительным требованиям
Чтобы создать правила автомасштабирования, вам понадобится имеющийся масштабируемый набор виртуальных машин. Вы можете создать его с помощью [портала Azure](virtual-machine-scale-sets-create-portal.md), [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) или [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Чтобы упростить создание правил автомасштабирования, определите некоторые переменные для масштабируемого набора. В примере ниже определены переменные для масштабируемого набора *myScaleSet* в группе ресурсов *myResourceGroup* в регионе *eastus*. Получить идентификатор подписки можно с помощью команды [az account show](/cli/azure/account#az_account_show). Если с вашей учетной записью связано несколько подписок, вернется только первая подписка. Измените имена и идентификатор подписки, как показано ниже:

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>Определение профиля автомасштабирования
Правила автомасштабирования развертываются в виде JSON (нотация объектов JavaScript) с помощью Azure CLI 2.0. Полный код JSON, определяющий и развертывающий правила автомасштабирования, представлен далее в этой статье. 

Начало профиля автомасштабирования определяет используемую по умолчанию, минимальную и максимальную емкости масштабируемого набора. В примере ниже задаются минимальная и максимальная емкости (*2* и *10*), а также емкость по умолчанию экземпляров виртуальной машины.

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Создание правила для автоматического масштабирования
При увеличении потребностей приложения в масштабируемом наборе увеличивается нагрузка на экземпляры виртуальной машины. Если такая увеличенная нагрузка представляет собой не просто краткий спрос, а является согласованной, можно настроить правила автомасштабирования, чтобы увеличить число экземпляров виртуальной машины в масштабируемом наборе. После создания этих экземпляров виртуальных машин и развертывания приложений масштабируемые наборы запускают распределение трафика между ними через подсистему балансировки нагрузки. Вы можете контролировать, какие метрики отслеживать, например ЦП или диск, продолжительность соответствия нагрузки приложения заданному пороговому значению, а также количество экземпляров виртуальной машины для добавления в масштабируемый набор.

Давайте создадим правило, в соответствии с которым количество экземпляров виртуальных машин в масштабируемом наборе увеличивается, когда средняя загрузка ЦП превышает 70 % в течение 10 минут. При активации правила количество экземпляров виртуальной машины увеличивается на 20 %. В масштабируемых наборах с небольшим числом экземпляров виртуальных машин можно для параметра `type` задать значение*ChangeCount* и увеличить значение `value` на *1* или *2* экземпляра. В этих же наборах с большим числом экземпляров виртуальных машин более оптимальным является увеличение на 10 или 20 % экземпляров.

Ниже приведены параметры, используемые для этого правила.

| Параметр         | Пояснение                                                                                                         | Значение           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Метрика производительности для мониторинга и применения действий в масштабируемом наборе.                                                   | Percentage CPU  |
| *timegrain*       | Определяет, как часто собираются показатели для анализа.                                                                   | 1 минута        |
| *timeAggregation* | Определяет способ вычисления собранных метрик для анализа.                                                | Среднее         |
| *timeWindow*      | Количество времени, в течение которого выполняется отслеживание перед сравнением значений метрик и пороговых значений.                                   | 10 минут      |
| *operator*        | Оператор для сравнения данных метрики с пороговым значением.                                                     | Больше чем    |
| *threshold*       | Значение, при достижении которого правило автомасштабирования запускает действие.                                                      | 70 %             |
| *direction*       | Определяет действие, применяемое к масштабируемому набору при выполнении условий правила: увеличение или уменьшение числа экземпляров.                                             | Увеличить        |
| *type*            | Указывает, что количество экземпляров виртуальных машин необходимо изменить, использовав процентное соотношение.                                 | Процентное изменение  |
| *значение*           | Указывает, сколько экземпляров виртуальных машин следует добавить или удалить, когда применяется правило.                                            | 20              |
| *cooldown*        | Время ожидания перед повторным применением правила, чтобы обеспечить время для активации действий автомасштабирования. | 5 мин       |

В следующем примере определяется правило увеличения количества экземпляров виртуальных машин. В *metricResourceUri* используются переменные, определенные ранее для идентификатора подписки, имени группы ресурсов и имени масштабируемого набора.

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Создание правила для автоматического уменьшения числа экземпляров
В вечерние часы или выходные дни потребность в приложении может снизиться. Если такая сниженная нагрузка является согласованной в течение некоторого периода времени, можно настроить правила автомасштабирования, чтобы уменьшить число экземпляров виртуальных машин в масштабируемом наборе. Это действие снижает стоимость запуска масштабируемого набора, так как вы запускаете только то количество экземпляров, которое необходимо для удовлетворения текущего спроса.

Создайте еще одно правило, в соответствии с которым количество экземпляров виртуальной машины в масштабируемом наборе уменьшается, когда средняя загрузка ЦП не превышает 30 % в течение 10 минут. В следующем примере определяется правило увеличения количества экземпляров виртуальных машин. В *metricResourceUri* используются переменные, определенные ранее для идентификатора подписки, имени группы ресурсов и имени масштабируемого набора.

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Применение правил автомасштабирования к масштабируемому набору
Остается применить профиль и правила автомасштабирования к масштабируемому набору. Затем станет возможным автоматическое увеличение и уменьшение масштаба в зависимости от потребностей приложения. Примените профиль автомасштабирования командой [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create), как показано ниже. В полном коде JSON используются профиль и правила, указанные в предыдущих разделах.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Мониторинг количества экземпляров в масштабируемом наборе
Чтобы узнать количество и состояние экземпляров виртуальных машин, просмотрите список экземпляров в масштабируемом наборе, используя команду [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances). Состояние указывает на подготовку экземпляра виртуальной машины по мере автоматического увеличения масштаба масштабируемого набора или отзыв подготовки по мере автоматического уменьшения масштаба. В примере ниже приведено состояние экземпляра виртуальной машины для масштабируемого набора с именем *myScaleSet* в группе ресурсов с именем *myResourceGroup*:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>Автомасштабирование на основе расписания
В примерах выше мы рассмотрели автоматическое масштабирование масштабируемого набора на основе основных метрик узла, например использования ЦП. Можно также создать правила автомасштабирования на основе расписания. Эти правила на основе расписания позволяют автоматически увеличивать количество экземпляров виртуальных машин перед ожидаемым увеличением потребностей приложения, например основными рабочими часами, а затем автоматически уменьшить количество экземпляров, когда потребность в использовании приложения снижается, например на выходных.

Чтобы использовать правила автомасштабирования на основе расписания, создайте профиль JSON, определяющий количество экземпляров виртуальных машин, запускаемых на интервале времени с фиксированным началом и концом. В следующем примере определяется правило для увеличения числа экземпляров до *10* в *9:00* по рабочим дням (с понедельника по пятницу).

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

Чтобы уменьшить число экземпляров вечером, создайте другое правило, которое определяет меньшее число экземпляров виртуальных машин и соответствующее время начала интервала.

Приведенный ниже полный пример определяет правила для увеличения и последующего уменьшения масштаба, а затем применяет профиль автомасштабирования с помощью команды [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). Этот пример перезаписывает правила автомасштабирования на основе метрик, созданные в предыдущих примерах. В *metricResourceUri* используются переменные, определенные ранее для идентификатора подписки, имени группы ресурсов и имени масштабируемого набора.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>Дополнительная информация
Из этой статьи вы узнали, как использовать правила автомасштабирования для горизонтального масштабирования, а также как увеличивать или уменьшать *число* экземпляров виртуальных машин в масштабируемом наборе. Чтобы увеличить или уменьшить *размер* экземпляров виртуальных машин, вы также можете выполнить вертикальное масштабирование. Дополнительные сведения см. в статье [Вертикальное автомасштабирование масштабируемых наборов виртуальных машин](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Сведения об управлении экземплярами виртуальных машин см. в статье [Управление масштабируемым набором виртуальных машин с помощью Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Сведения о том, как создать оповещения при активации правил автомасштабирования, см. в статье [Использование действий автомасштабирования для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Вы также можете [использовать журналы аудита для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
