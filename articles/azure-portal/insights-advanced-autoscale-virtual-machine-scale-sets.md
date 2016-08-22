.<properties
	pageTitle="Azure Insights. Расширенная настройка автомасштабирования с помощью шаблонов Resource Manager для набора масштабирования виртуальных машин | Microsoft Azure"
	description="Настройка автомасштабирования для наборов масштабирования виртуальных машин на основе нескольких правил и профилей с уведомлением о действиях масштабирования с помощью электронной почты и webhoook."
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

.<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="ashwink"/>

# Расширенная настройка автомасштабирования с помощью шаблонов Resource Manager для набора масштабирования виртуальных машин

Можно масштабировать наборы масштабирования виртуальных машин (VMSS) на основе пороговых значений метрик производительности по расписанию или в определенную дату. Можно также настроить уведомления с помощью электронной почты и webhook для действий масштабирования. В этом пошаговом руководстве будет показан пример настройки всего вышеперечисленного для набора масштабирования виртуальных машин с помощью шаблона Resource Manager.

>[AZURE.NOTE] Хотя в этом пошаговом руководстве приведены указания для наборов масштабирования виртуальных машин, их можно применить и для автомасштабирования облачных служб и веб-приложений. Если вы хотите использовать простые параметры масштабирования набора масштабирования виртуальных машин, основанные на простой метрике производительности, например метрике ЦП, обратитесь к документации по [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) и [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).



## Пошаговое руководство
В этом пошаговом руководстве для настройки и обновления конфигурации автомасштабирования VMSS используется [Azure Resource Explorer](https://resources.azure.com/). Azure Resource Explorer — простой инструмент управления ресурсами Azure с помощью шаблонов Resource Manager. Если вы еще не работали с Azure Resource Explorer, ознакомьтесь с [этим введением](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Разверните новый VMSS с базовой конфигурацией автомасштабирования. В этой статье используется конфигурация из коллекции быстрого запуска Azure, в которой есть VMSS Windows с базовым шаблоном автомасштабирования. То же самое относится и к экземплярам VMSS на основе Linux.

2. После создания VMSS перейдите к ресурсу VMSS из Azure Resource Explorer. Под узлом Microsoft.Insights вы увидите следующую структуру.

	.![Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

	С помощью шаблона была создана конфигурация автомасштабирования по умолчанию **autoscalewad**. Справа можно просмотреть полное определение этой конфигурации автомасштабирования. В данном случае в конфигурации автомасштабирования по умолчанию используется правило масштабирования на основе загрузки ЦП в процентах.

3. Теперь можно добавить профили и правила на основе расписания или специальных требований. Мы создадим конфигурацию автомасштабирования с тремя профилями. Чтобы узнать больше о профилях и правилах автомасштабирования, ознакомьтесь с разделом [Рекомендации по автомасштабированию](./insights-autoscale-best-practices.md).

    | Профили и правила | Описание |
	|---------|-------------------------------------|
	| **Профиль** | **На основе производительности или метрики** |
	| правило; | Количество сообщений в очереди служебной шины > x |
	| правило; | Количество сообщений в очереди служебной шины < y |
	| правило; | % загрузки ЦП < n |
	| правило; | % загрузки ЦП < p |
	| **Профиль** | **Утренние часы в рабочие дни (без правил)** |
	| **Профиль** | **День запуска продукта (без правил)** |

4. Ниже приведен гипотетический сценарий масштабирования, который будет использоваться в данном пошаговом руководстве.
	- _**На основе нагрузки** — мне хотелось бы, чтобы масштаб изменялся соответственно нагрузке на мое приложение, размещенное в VMSS._
	- _**Размер очереди сообщений** — я использую очередь служебной шины для входящих сообщений, поступающих в приложение. Я использую количество сообщений в очереди и загрузку ЦП в процентах и настрою профиль по умолчанию для активации действия масштабирования в случае, если количество сообщений или загрузка ЦП достигнет порогового значения._
	- _**День недели и время суток** — мне нужен профиль для еженедельного повторения в определенное время дня, который называется "Утренние часы в рабочие дни". На основе хронологических данных я знаю, что в это время лучше иметь определенное число экземпляров виртуальных машин для обработки нагрузки на приложение._
	- _**Особые даты** — мной добавлен профиль "День запуска продукта". Я заранее планирую особые даты, чтобы мое приложение было готово к обработке нагрузки после публикации рекламных объявлений или при добавлении нового продукта в приложение._
	- _Последние два профиля могут также содержать другие правила на основе метрик производительности, но в моем случае было решено не добавлять их и использовать правила на основе метрик производительности по умолчанию. Правила являются необязательными для профилей на основе повторения и дат._

	Определение приоритетов правил и профилей в механизме автомасштабирования описано также в статье [Рекомендации по автомасштабированию Azure Insights](insights-autoscale-best-practices.md). Список стандартных метрик для автомасштабирования см. в разделе [Общие метрики автомасштабирования Azure Insights](insights-autoscale-common-metrics.md).

5. Убедитесь, что в Resource Explorer включен режим **Read/Write** (Чтение и запись).

	.![Autoscalewad, конфигурация автомасштабирования по умолчанию](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Щелкните "Edit" (Изменить). **Замените** элемент profiles в конфигурации автомасштабирования следующим текстом.

	.![профили](./media/insights-advanced-autoscale-vmss/profiles.png)

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
	              "metricName": "\\Processor(_Total)\\% Processor Time",
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
	              "metricName": "\\Processor(_Total)\\% Processor Time",
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
	              "direction": "Increase",
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
	Описание поддерживаемых полей и их значений см. в [документации по REST API автомасштабирования](https://msdn.microsoft.com/ru-RU/library/azure/dn931928.aspx).

	Теперь конфигурация автомасштабирования содержит 3 профиля, описанных выше.

7. 	Наконец, давайте рассмотрим раздел **notification** конфигурации автомасштабирования. Уведомления об автомасштабировании позволяют выполнить три действия при активации масштабирования:

	1. уведомить администратора и соадминистраторов подписки;

	2. от править электронное сообщение набору пользователей;

	3. активировать вызов webhook. При активации этот webhook отправит метаданные об условии автомасштабирования и ресурсе VMSS. Чтобы узнать больше о полезных данных webhook автомасштабирования, ознакомьтесь с разделом [Использование действий автомасштабирования для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Insights](./insights-autoscale-to-webhook-email.md).

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

## Дальнейшие действия

Воспользуйтесь следующими ссылками, чтобы получить дополнительную информацию об автомасштабировании.

[Стандартные метрики для автомасштабирования](./insights-autoscale-common-metrics.md)

[Рекомендации по автомасштабированию Azure](./insights-autoscale-best-practices.md)

[Управление автомасштабированием с помощью PowerShell](./insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Управление автомасштабированием с помощью интерфейса командной строки](./insights-cli-samples.md#autoscale)

[Настройка уведомлений с помощью webhook и электронной почты для автомасштабирования](./insights-autoscale-to-webhook-email.md)

<!---HONumber=AcomDC_0810_2016-->