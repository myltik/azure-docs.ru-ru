---
title: "Примеры для быстрого начала работы с интерфейсом командной строки 1.0 в Azure Monitor | Документация Майкрософт"
description: "Примеры команд интерфейса командной строки 1.0 для функций Azure Monitor. Azure Monitor — это служба Microsoft Azure, которая позволяет отправлять оповещения и осуществлять вызов URL-адресов на основе значений настроенных данных телеметрии, а также выполнять автоматическое масштабирование облачных служб, виртуальных машин и веб-приложений."
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: ec4512500dc3c77a40d2ebd1e6b460d5bb005811
ms.contentlocale: ru-ru
ms.lasthandoff: 08/10/2017

---
# <a name="azure-monitor--cross-platform-cli-10-quick-start-samples"></a>Примеры команд для кроссплатформенного интерфейса командной строки 1.0 в Azure Monitor
В этой статье приведены примеры команд интерфейса командной строки для работы с функциями Azure Monitor. Azure Monitor позволяет выполнять автомасштабирование облачных служб, виртуальных машин и веб-приложений, отправлять оповещения и осуществлять вызов URL-адресов на основе значений настроенных данных телеметрии.

> [!NOTE]
> Azure Monitor — это новое название для Azure Insights, актуальное с 25 сентября 2016 г. При этом пространства имен и соответствующие команды будут по-прежнему содержать фрагмент старого названия ("insights").
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Если вы еще не установили интерфейс командной строки Azure, обратитесь к разделу [Установка интерфейса командной строки Azure](../cli-install-nodejs.md). Если вы не знакомы с интерфейсом командной строки Azure, прочтите о нем подробнее в разделе [Использование интерфейса командной строки Azure для Mac, Linux и Windows с помощью Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

В Windows установите npm с [веб-сайта Node.js](https://nodejs.org/). После завершения установки, запустив CMD.exe с привилегиями администратора, выполните следующую команду из папки, в которой установлена npm:

```console
npm install azure-cli --global
```

Затем перейдите в необходимый каталог или расположение и в командной строке введите следующее:

```console
azure help
```

## <a name="log-in-to-azure"></a>Вход в Azure
Первый шаг — войти в учетную запись Azure.

```console
azure login
```

После выполнения этой команды необходимо будет войти, следуя инструкциям на экране. После этого вы увидите учетную запись, идентификатор клиента и идентификатор подписки по умолчанию. Все команды работают в контексте подписки по умолчанию.

Для получения сведений о текущей подписке используйте следующую команду.

```console
azure account show
```

Чтобы изменить подписку для рабочего контекста, используйте следующую команду.

```console
azure account set "subscription ID or subscription name"
```

Для использования команд Azure Resource Manager и Azure Monitor необходимо войти в режим Azure Resource Manager.

```console
azure config mode arm
```

Чтобы просмотреть список всех поддерживаемых команд Azure Monitor, выполните следующие действия.

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>Просмотр журнала действий для подписки
Чтобы просмотреть события журнала действий, выполните шаги ниже.

```console
azure insights logs list [options]
```

Попробуйте выполнить следующие действия, чтобы просмотреть все доступные параметры.

```console
azure insights logs list -help
```

Ниже приведен пример получения списка журналов для группы ресурсов

```console
azure insights logs list --resourceGroup "myrg1"
```

Пример получения списка журналов для вызывающего объекта

```console
azure insights logs list --caller "myname@company.com"
```

Пример получения списка журналов для вызывающего объекта и типа ресурса с датами начала и окончания

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Работа с оповещениями
В этом разделе описана работа с оповещениями.

### <a name="get-alert-rules-in-a-resource-group"></a>Получение правил генерации оповещений в группе ресурсов
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Создание правила генерации оповещения для метрики
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-webtest-alert-rule"></a>Создание правила генерации оповещения для веб-теста
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Удаление правила генерации оповещения
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Профили журнала
В этом разделе описана работа с профилями журнала.

### <a name="get-a-log-profile"></a>Получение профиля журнала
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Добавление профиля журнала без хранения
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Удаление профиля журнала
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Добавление профиля журнала с хранением
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Добавление профиля журнала с хранением и концентратором событий
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Диагностика
В этом разделе описана работа с параметрами диагностики.

### <a name="get-a-diagnostic-setting"></a>Получение параметра диагностики
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Отключение параметра диагностики
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Включение параметра диагностики без хранения
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoscale
В этом разделе описана работа с параметрами автомасштабирования. Эти примеры необходимо изменить.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Получение параметров автомасштабирования для группы ресурсов
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Получение параметров автомасштабирования по имени в группе ресурсов
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Установка параметров автомасштабирования
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

