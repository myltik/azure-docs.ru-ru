---
title: Расширенное автомасштабирование с помощью виртуальных машин Azure | Документация Майкрософт
description: Использование Resource Manager и Масштабируемых наборов ВМ с несколькими правилами и профилями, которые отправляют сообщения электронной почты и вызывают URL-адреса объектов webhook с действиями масштабирования.
author: anirudhcavale
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 7e3576e2-4a2b-4736-b5ae-98c4689cdd2b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2016
ms.author: ancav
ms.openlocfilehash: 80955535c8d863cd3d8d1b77e2ab8bc016b6d9f3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Расширенная настройка автомасштабирования с помощью шаблонов Resource Manager для набора масштабирования виртуальных машин
Масштабируемые наборы виртуальных машин можно свертывать и развертывать на основе пороговых значений метрик производительности по расписанию или на определенную дату. Можно также настроить уведомления с помощью электронной почты и webhook для действий масштабирования. В этом пошаговом руководстве показан пример настройки всех этих объектов для масштабируемого набора виртуальных машин с помощью шаблона Resource Manager.

> [!NOTE]
> Хотя в этом пошаговом руководстве приведены сведения для масштабируемых наборов виртуальных машин, их можно применить и для автомасштабирования [облачных служб](https://azure.microsoft.com/services/cloud-services/) и [веб-приложений службы приложений](https://azure.microsoft.com/services/app-service/web/).
> Простые параметры масштабирования набора масштабирования виртуальных машин, основанные на простой метрике производительности, например метрике ЦП, описаны в документации по [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) и [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).
>
>

## <a name="walkthrough"></a>Пошаговое руководство
В этом пошаговом руководстве для настройки и обновления конфигурации автомасштабирования набора масштабирования используется [Azure Resource Explorer](https://resources.azure.com/). Azure Resource Explorer — простой инструмент управления ресурсами Azure с помощью шаблонов Resource Manager. Если вы еще не работали с Azure Resource Explorer, ознакомьтесь с [этой информацией](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Разверните новый набор масштабирования с базовой конфигурацией автомасштабирования. В этой статье используется конфигурация из коллекции быстрого запуска Azure, в которой есть набор масштабирования Windows с базовым шаблоном автомасштабирования. Наборы масштабирования Linux работают точно так же.
2. После создания набора масштабирования перейдите к его ресурсу из Azure Resource Explorer. Под узлом Microsoft.Insights вы увидите следующую структуру.

    ![Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

    С помощью шаблона была создана конфигурация автомасштабирования по умолчанию **autoscalewad**. Справа можно просмотреть полное определение этой конфигурации автомасштабирования. В данном случае в конфигурации автомасштабирования по умолчанию используется правило масштабирования на основе загрузки ЦП в процентах.  

3. Теперь можно добавить профили и правила на основе расписания или специальных требований. Мы создадим конфигурацию автомасштабирования с тремя профилями. Чтобы узнать больше о профилях и правилах автомасштабирования, ознакомьтесь со статьей [Рекомендации по автомасштабированию Azure Insights](insights-autoscale-best-practices.md).  

    | Профили и правила | ОПИСАНИЕ |
    |--- | --- |
    | **Профиль** |**На основе производительности или метрики** |
    | правило; |Количество сообщений в очереди служебной шины > x |
    | правило; |Количество сообщений в очереди служебной шины < y |
    | правило; |% загрузки ЦП > n |
    | правило; |% загрузки ЦП < p |
    | **Профиль** |**Утренние часы в рабочие дни (без правил)** |
    | **Профиль** |**День запуска продукта (без правил)** |

4. Ниже приведен гипотетический сценарий масштабирования, который используется в данном пошаговом руководстве.

    * **На основе нагрузки** — мне хотелось бы, чтобы масштаб изменялся соответственно нагрузке на мое приложение, размещенное в масштабируемом наборе.*
    * **Размер очереди сообщений** — я использую очередь служебной шины для входящих сообщений, поступающих в приложение. Я использую количество сообщений в очереди и загрузку ЦП в процентах и настрою профиль по умолчанию для активации действия масштабирования в случае, если количество сообщений или загрузка ЦП достигнет порогового значения.*
    * **День недели и время суток** — мне нужен профиль для еженедельного повторения в определенное время дня, который называется "Утренние часы в рабочие дни". На основе хронологических данных я знаю, что в это время лучше иметь определенное число экземпляров виртуальных машин для обработки нагрузки на приложение.*
    * **Особые даты** — добавлен профиль "День запуска продукта". Я заранее планирую особые даты, чтобы мое приложение было готово к обработке нагрузки после публикации рекламных объявлений или при добавлении нового продукта в приложение.*
    * *Последние два профили могут также содержать другие правила на основе метрик производительности. В данном случае было решено не добавлять их, а положиться на правила, основанные на метриках производительности по умолчанию. Правила являются необязательными для профилей на основе повторения и дат.*

    Определение приоритетов правил и профилей в механизме автомасштабирования описано также в статье [Рекомендации по автомасштабированию Azure Insights](insights-autoscale-best-practices.md).
    Список стандартных метрик для автомасштабирования см. в статье [Общие метрики автомасштабирования Azure Insights](insights-autoscale-common-metrics.md).

5. Убедитесь, что в Resource Explorer включен режим **Read/Write** (Чтение и запись).

    ![Autoscalewad, конфигурация автомасштабирования по умолчанию](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Нажмите кнопку «Изменить». **Замените** элемент profiles в конфигурации автомасштабирования следующим текстом:

    ![профили](./media/insights-advanced-autoscale-vmss/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
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
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
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
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
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
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Описание поддерживаемых полей и их значений см. в [документации по REST API автомасштабирования](https://msdn.microsoft.com/en-us/library/azure/dn931928.aspx). Теперь конфигурация автомасштабирования содержит три профиля, описанных выше.

7. Наконец, рассмотрим раздел **notification** конфигурации автомасштабирования. Уведомления об автомасштабировании позволяют выполнить три действия при активации масштабирования:
   - уведомить администратора и соадминистраторов подписки;
   - от править электронное сообщение набору пользователей;
   - активировать вызов webhook. При активации этот webhook отправляет метаданные об условии автомасштабирования и ресурсе набора масштабирования. Чтобы узнать больше о полезных данных webhook автомасштабирования, ознакомьтесь со статьей разделом [Использование действий автомасштабирования для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Insights](insights-autoscale-to-webhook-email.md).

   Добавьте следующий текст в конфигурацию автомасштабирования, заменив элемент **notification**, имеющий значение NULL.

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Нажмите кнопку **Put** (Поместить) в Resource Explorer, чтобы обновить конфигурацию автомасштабирования.

Вы обновили конфигурацию автомасштабирования для набора масштабирования виртуальных машин, добавив в нее несколько профилей и уведомления.

## <a name="next-steps"></a>Дальнейшие действия
Воспользуйтесь следующими ссылками, чтобы получить дополнительную информацию об автомасштабировании.

[Устранение неполадок при автомасштабировании масштабируемых наборов виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Общие метрики автомасштабирования Azure Insights](insights-autoscale-common-metrics.md)

[Рекомендации по автомасштабированию Azure Insights](insights-autoscale-best-practices.md)

[Создание параметров автомасштабирования и управление ими](insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Автомасштабирование](insights-cli-samples.md#autoscale)

[Использование действий автомасштабирования для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Insights](insights-autoscale-to-webhook-email.md)
