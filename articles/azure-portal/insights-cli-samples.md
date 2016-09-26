.<properties
	pageTitle="Azure Insights: примеры команд для Azure Insights | Microsoft Azure"
	description="Примеры команд для доступа к функциям мониторинга Azure Insights. Azure Insights — это служба Microsoft Azure, которая позволяет выполнять автоматическое масштабирование облачных служб, виртуальных машин и веб-приложений, отправлять оповещения и осуществлять вызов URL-адресов на основе значений настроенных данных телеметрии."
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="ashwink"/>

# Примеры команд для межплатформенного интерфейса командной строки Azure Insights

В этой статье приведены примеры команд интерфейса командной строки для работы с функциями мониторинга Azure Insights. Azure Insights позволяет выполнять автомасштабирование облачных служб, виртуальных машин и веб-приложений, отправлять оповещения и осуществлять вызов URL-адресов на основе значений настроенных данных телеметрии.


## Предварительные требования

Если вы еще не установили интерфейс командной строки Azure, обратитесь к разделу [Установка интерфейса командной строки Azure](../xplat-cli-install.md). Если вы не знакомы с интерфейсом командной строки Azure, прочтите о нем подробнее в разделе [Использование интерфейса командной строки Azure для Mac, Linux и Windows с помощью Azure Resource Manager](../xplat-cli-azure-resource-manager.md).


В Windows установите npm с [веб-сайта Node.js](https://nodejs.org/). После завершения установки, запустив CMD.exe с привилегиями администратора, выполните следующую команду из папки, в которой установлена npm:

```
npm install azure-cli --global
```

Затем перейдите в необходимый каталог или расположение и в командной строке введите следующее:

```
azure help
```

## Вход в Azure

Первый шаг — войти в учетную запись Azure.

```
azure login
```

После выполнения этой команды необходимо будет войти, следуя инструкциям на экране. После этого вы увидите учетную запись, идентификатор клиента и идентификатор подписки по умолчанию. Все команды работают в контексте подписки по умолчанию.

Для получения сведений о текущей подписке используйте следующую команду.

```
azure account show
```

Чтобы изменить подписку для рабочего контекста, используйте следующую команду.

```
azure account set "subscription ID or subscription name"
```

Для использования команд Azure Resource Manager и Azure Insights необходимо находиться в режиме Azure Resource Manager.

```
azure config mode arm
```

Чтобы просмотреть список всех поддерживаемых команд Azure Insights, выполните следующие действия.

```
azure insights
```

## Просмотр журналов аудита для подписки

Чтобы просмотреть список журналов аудита, выполните следующие действия.

```
azure insights logs list [options]
```

Попробуйте выполнить следующие действия, чтобы просмотреть все доступные параметры.

```
azure insights logs list -help
```

Ниже приведен пример получения списка журналов для группы ресурсов

```
azure insights logs list --resourceGroup "myrg1"
```

Пример получения списка журналов для вызывающего объекта

```
azure insights logs list --caller "myname@company.com"
```

Пример получения списка журналов для вызывающего объекта и типа ресурса с датами начала и окончания

```
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## Работа с оповещениями
В этом разделе описана работа с оповещениями.

### Получение правил генерации оповещений в группе ресурсов

```
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### Создание правила генерации оповещения для метрики

```
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### Создание правила генерации оповещения для журнала

```
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### Создание правила генерации оповещения для веб-теста

```
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### Удаление правила генерации оповещения

```
azure insights alerts rule delete abhingrgtest123 andy0323
```

## Профили журнала
В этом разделе описана работа с профилями журнала.

### Получение профиля журнала

```
azure insights logprofile list
azure insights logprofile get -n default
```


### Добавление профиля журнала без хранения

```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Удаление профиля журнала

```
azure insights logprofile delete --name default
```

### Добавление профиля журнала с хранением

```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### Добавление профиля журнала с хранением и концентратором событий

```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## Диагностика
В этом разделе описана работа с параметрами диагностики.

### Получение параметра диагностики

```
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### Отключение параметра диагностики

```
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### Включение параметра диагностики без хранения

```
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## Autoscale
В этом разделе описана работа с параметрами автомасштабирования. Эти примеры необходимо изменить.

### Получение параметров автомасштабирования для группы ресурсов

```
azure insights autoscale setting list montest2
```

### Получение параметров автомасштабирования по имени в группе ресурсов

```
azure insights autoscale setting list montest2 -n setting2
```


### Установка параметров автомасштабирования

```
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

<!---HONumber=AcomDC_0914_2016-->