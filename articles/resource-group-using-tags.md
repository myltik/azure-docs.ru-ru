.<properties
	pageTitle="Использование тегов для организации ресурсов Azure | Microsoft Azure"
	description="Здесь описано, как применить теги, чтобы организовать ресурсы для выставления счетов и управления."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="tomfitz"/>


# Использование тегов для организации ресурсов в Azure

Диспетчер ресурсов помогает логически организовать ресурсы с помощью тегов. Теги состоят из пар ключ —значение, которые определяют ресурсы с помощью заданных вами свойств. Чтобы пометить ресурсы как относящиеся к одной категории, примените к ним одинаковый тег.

При просмотре ресурсов с определенным тегом отображаются ресурсы из всех групп. Вы не ограничены только ресурсами из одной группы. Это позволяет организовать их независимо от связей развертывания. Теги могут быть полезными, когда требуется организовать ресурсы для выставления счетов или управления.

Каждый тег, добавляемый в ресурс или группу ресурсов, автоматически добавляется в таксономию всей подписки. Вы можете предварительно заполнить таксономию подписки именами тегов и значениями, которые будут использоваться в будущем, когда ресурсы будут маркированы тегами.

Каждая группа ресурсов может иметь не более 15 тегов. Имя тега ограничено 512 символами, а значение тега — 256 символами.

> [AZURE.NOTE] Теги можно применять только к ресурсам, которые поддерживают операции диспетчера ресурсов. Если вы создали виртуальную машину, виртуальную сеть или хранилище при помощи модели классического развертывания (например, через классический портал), то к таким ресурсам нельзя применить теги. Для поддержки тегов эти ресурсы необходимо развернуть повторно с помощью Resource Manager. Все остальные ресурсы поддерживают теги.

## Шаблоны

Для маркировки ресурса во время развертывания просто добавьте элемент **tags** к развертываемому ресурсу и укажите имя и значение тега. Имя и значение тега не должны заранее существовать в вашей подписке. Можно указать до 15 тегов для каждого ресурса.

В следующем примере показана учетная запись хранения с тегом.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Сейчас диспетчер ресурсов не поддерживает обработку объекта для имен и значений тегов. Вместо этого передайте объект со значениями тегов, но по-прежнему укажите имена тегов, как показано в примере ниже.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## Microsoft Azure

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## Инфраструктура CLI Azure

Существующие теги находятся непосредственно в ресурсах и группах ресурсов. Чтобы просмотреть существующие теги, просто запросите сведения о группе ресурсов и ее ресурсах с помощью команды **azure group show**.

    azure group show -n tag-demo-group
    
Отобразятся метаданные о группе ресурсов, включая все примененные к ней теги.
    
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/{guid}/resourceGroups/tag-demo-group
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production
    data:    Resources:
    data:
    data:      Id      : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    data:      Name    : tfsqlserver
    data:      Type    : servers
    data:      Location: eastus2
    data:      Tags    : Dept=Finance;Environment=Production
    ...

Для получения тегов только для группы ресурсов используйте служебную программу JSON, например [jq](http://stedolan.github.io/jq/download/).

    azure group show -n tag-demo-group --json | jq ".tags"
    
Отобразятся теги для данной группы ресурсов.
    
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

Теги для конкретного ресурса можно просмотреть с помощью команды **azure resource show**.

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    
Отобразятся теги для данного ресурса.
    
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
В приведенном ниже примере показано, как получить сведения о всех ресурсах, имеющих имя и значение тега.

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    
Отобразятся имена ресурсов с таким тегом.
    
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

При этом обновляются все теги. Чтобы добавить один тег к ресурсу, который уже содержит теги, получите сведения о всех существующих тегах, которые хотите сохранить. Чтобы задать значения тегов для группы ресурсов, используйте команду **azure group set** и укажите все теги для группы ресурсов.

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    
Отобразится сводка по группе ресурсов с новыми тегами.
    
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
Можно вывести список существующих тегов в подписке с помощью команды **azure tag list** и добавить тег с помощью команды **azure tag create**. Чтобы удалить тег из таксономии подписки, сначала удалите этот тег из всех ресурсов. А затем удалите его с помощью команды **azure tag delete**.

## Интерфейс REST API

Как на портале, так и в PowerShell «за кулисами» используется [интерфейс REST API диспетчера ресурсов](https://msdn.microsoft.com/library/azure/dn848368.aspx). Если требуется интегрировать теги в другую среду, их можно получить с помощью метода GET по идентификатору ресурса и обновить набор тегов с помощью вызова метода PATCH.


## Теги и выставление счетов

Для поддерживаемых служб можно использовать теги, чтобы сгруппировать данные выставления счетов. Например, [виртуальные машины, интегрированные с диспетчером ресурсов Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md), позволяют определить и применить теги, чтобы упорядочить сведения об использовании выставления счетов для виртуальных машин. Если вы используете несколько виртуальных машин для различных организаций, можно использовать теги для группирования сведений об использовании по месту возникновения затрат. Теги также можно использовать для упорядочивания затрат по среде выполнения, например сведения об использовании выставления счетов для виртуальных машин, запущенных в рабочей среде.

Сведения о тегах можно получить с помощью [интерфейсов API использования ресурсов Azure и RateCard](billing-usage-rate-card-overview.md) или файла сведений об использовании с разделителями-запятыми (CSV-файла). Этот файл можно скачать на [портале учетных записей Azure](https://account.windowsazure.com/) или на [портале EA](https://ea.azure.com). Дополнительные сведения о программном доступе к данным для выставления счетов см. в разделе [Дополнительные сведения о потреблении ресурсов Microsoft Azure](billing-usage-rate-card-overview.md). Подробнее об операциях REST API см. в статье [Справочник по REST API выставления счетов Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

При скачивании CSV-файла сведений об использовании для служб, поддерживающих теги выставления счетов, эти теги отображаются в столбце **Теги**. Дополнительные сведения см. в разделе [Расшифровка счета за использование Microsoft Azure](billing-understand-your-bill.md).

![См. сведения об использовании тегов при выставлении счетов.](./media/resource-group-using-tags/billing_csv.png)

## Дальнейшие действия

- Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. При использовании определяемой политики может потребоваться, чтобы для всех ресурсов было задано значение тега. Дополнительные сведения см. в статье [Применение политик для управления ресурсами и контроля доступа](resource-manager-policy.md).
- Общие сведения об использовании Azure PowerShell для развертывания ресурсов см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](./powershell-azure-resource-manager.md).
- Основные сведения об использовании Azure CLI для развертывания ресурсов см. в статье [Использование Azure CLI для Mac, Linux и Windows со службой управления ресурсами Azure](./xplat-cli-azure-resource-manager.md).
- Общие сведения об использовании портала см. в статье [Управление ресурсами Azure с помощью портала Azure](./azure-portal/resource-group-portal.md).

<!---HONumber=AcomDC_0817_2016-->