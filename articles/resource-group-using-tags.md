<properties
	pageTitle="Использование тегов для организации ресурсов Azure | Microsoft Azure"
	description="Здесь описано, как применить теги, чтобы организовать ресурсы для выставления счетов и управления."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="tomfitz"/>


# Использование тегов для организации ресурсов в Azure

Диспетчер ресурсов помогает логически организовать ресурсы с помощью тегов. Теги состоят из пар ключ —значение, которые определяют ресурсы с помощью заданных вами свойств. Чтобы пометить ресурсы как относящиеся к одной категории, примените к ним одинаковый тег.

При просмотре ресурсов с определенным тегом отображаются ресурсы из всех групп. Вы не ограничены только ресурсами из одной группы. Это позволяет организовать их независимо от связей развертывания. Теги особенно помогут, когда требуется организовать ресурсы для выставления счетов или управления.

Каждый тег, добавляемый в ресурс или группу ресурсов, автоматически добавляется в таксономию всей подписки. Вы можете предварительно заполнить таксономию подписки именами тегов и значениями, которые будут использоваться в будущем, когда ресурсы будут маркированы тегами.

Каждая группа ресурсов может иметь не более 15 тегов. Имя тега ограничено 512 символами, а значение тега — 256 символами.

> [AZURE.NOTE] Теги можно применять только к ресурсам, которые поддерживают операции диспетчера ресурсов. Если вы создали виртуальную машину, виртуальную сеть или хранилище при помощи модели классического развертывания (например, через классический портал или интерфейс API управления службами), к таким ресурсам нельзя применить теги. Для поддержки тегов эти ресурсы необходимо развернуть повторно с помощью диспетчера ресурсов. Все остальные ресурсы поддерживают теги.

## Теги в шаблонах

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

Сейчас диспетчер ресурсов не поддерживает обработку объекта для имен и значений тегов. Вместо этого можно передать объект со значениями тегов, но по-прежнему необходимо указать имена тегов, как показано ниже.

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


## Теги на портале

На портале можно добавить теги к существующим ресурсам и группам ресурсов. Используйте узел "Обзор" для перехода к ресурсу или группе ресурсов, которые вы хотите отметить тегами, и щелкните "Теги" в разделе "Обзор" в верхней части модуля.

![м](./media/resource-group-using-tags/tag-icon.png)

После этого откроется модуль со списком уже примененных тегов. Если это ваш первый тег, список будет пустым. Для добавления тега укажите имя и значение, а затем нажмите клавишу ВВОД. Добавив несколько тегов, вы заметите варианты автозаполнения на основании уже существующих имен и значений тегов, которые помогают обеспечить согласованность таксономии в ваших ресурсах и избежать распространенных ошибок, например опечаток.

![Маркировка ресурсов тегами с парами "имя — значение"](./media/resource-group-using-tags/tag-resources.png)

Для просмотра таксономии тегов на портале используйте узел "Обзор" для просмотра всех элементов, а затем выберите "Теги".

![Поиск тегов с помощью узла "Обзор"](./media/resource-group-using-tags/browse-tags.png)

Закрепите наиболее важные теги на начальной панели для быстрого доступа к ним. Теперь вы готовы к работе. Желаем успехов!

![Закрепление тегов на начальной панели](./media/resource-group-using-tags/pin-tags.png)

## Теги и PowerShell

Теги хранятся непосредственно в ресурсах и группах ресурсов, поэтому, чтобы просмотреть, какие теги уже применены, мы можем просто получить ресурс или группу ресурсов с помощью **Get-AzureRmResource** или **Get-AzureRmResourceGroup**, соответственно. Давайте начнем с группы ресурсов.

    PS C:\> Get-AzureRmResourceGroup -Name tag-demo-group

    ResourceGroupName : tag-demo-group
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production


Этот командлет возвращает небольшой объем метаданных о группе ресурсов, включая информацию о примененных тегах, если они есть.

При получении метаданных для ресурса теги не отображаются напрямую. Ниже вы увидите, что теги отображаются только как объект Hashtable.

    PS C:\> Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

Чтобы просмотреть реальные теги, нужно извлечь свойство **Tags**.

    PS C:\> (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
    Dept: Finance
    Environment: Production

Вместо просмотра тегов для конкретного ресурса или группы ресурсов часто требуется получить все ресурсы или группы ресурсов, имеющие определенный тег и значение. Чтобы получить ресурсы или группы ресурсов с определенным тегом, используйте командлет **Find-AzureRmResourceGroup** с параметром **-Tag**.

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    tag-demo-group
    web-demo-group

Чтобы получить все ресурсы с определенным тегом и значением, используйте командлет **Find-AzureRmResource**.

    PS C:\> Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }
    tfsqlserver
    tfsqldatabase

Чтобы добавить тег к группе ресурсов, не имеющей существующих тегов, просто воспользуйтесь командой **Set-AzureRmResourceGroup** и укажите объект тега.

    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
С помощью команды **SetAzureRmResource** можно добавить теги к ресурсу, не имеющему существующих тегов.

    PS C:\> Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

Теги обновляются полностью, так что при добавлении одного тега к ресурсу, который уже содержит теги, вам потребуется использовать массив для сохранения всех необходимых тегов. Для этого сначала выберите существующие теги, добавьте в этот набор новый тег и повторно примените все теги.

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag $tags

Чтобы удалить тег, достаточно сохранить массив без него.

Для ресурсов этот процесс аналогичен, за исключением того, что используются командлеты **Get-AzureRmResource** и **Set-AzureRmResource**.

Чтобы получить список всех тегов в подписке с помощью PowerShell, используйте командлет **Get-AzureRmTag**.

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

Вы можете увидеть теги, начинающиеся с hidden- и link:. Это внутренние теги, которые вы должны игнорировать. Изменять их не следует.

С помощью командлета **New-AzureRmTag** в таксономию можно добавлять новые теги. Эти теги будут включены в автозаполнение, даже если они еще не были применены для ресурсов или групп ресурсов. Чтобы удалить имя или значение тега, сначала необходимо удалить тег из всех ресурсов, в которых он может применяться, а затем воспользоваться командлетом **Remove-AzureRmTag** для удаления тега из таксономии.

## Теги и CLI Azure

Теги находятся непосредственно в ресурсах и их группах, поэтому, чтобы просмотреть, какие теги уже применены, мы можем просто получить ресурс или группу ресурсов с помощью командлета **azure group show**.

    azure group show -n tag-demo-group
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

Для получения тегов только для группы ресурсов используйте программу JSON, например [jq](http://stedolan.github.io/jq/download/).

    azure group show -n tag-demo-group --json | jq ".tags"
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

Теги для конкретных ресурсов можно просмотреть с помощью командлета **azure resource show**.

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
Можно получить все ресурсы с определенным тегом и значением, как показано ниже.

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

Теги обновляются полностью, поэтому при добавлении одного тега к ресурсу, уже содержащему теги, вам потребуется извлечь все существующие теги, которые нужно сохранить. Чтобы задать значения тегов для группы ресурсов, используйте командлет **azure group set** и укажите все теги для группы ресурсов.

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
Можно вывести список существующих тегов в подписке с помощью командлета **azure tag list** и добавить новый тег с помощью командлета **azure tag create**. Чтобы удалить тег из таксономии подписки, сначала необходимо удалить тег из всех ресурсов, в которых он может применяться, а затем воспользоваться командлетом **azure tag delete**.

## Теги и REST API

Как на портале, так и в PowerShell «за кулисами» используется [интерфейс REST API диспетчера ресурсов](https://msdn.microsoft.com/library/azure/dn848368.aspx). Если требуется интегрировать теги в другую среду, их можно получить с помощью метода GET по идентификатору ресурса и обновить набор тегов с помощью вызова метода PATCH.


## Теги и выставление счетов

Для поддерживаемых служб можно использовать теги, чтобы сгруппировать данные выставления счетов. Например, [виртуальные машины, интегрированные с диспетчером ресурсов Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md), позволяют определить и применить теги, чтобы упорядочить сведения об использовании выставления счетов для виртуальных машин. Если вы используете несколько виртуальных машин для различных организаций, можно использовать теги для группирования сведений об использовании по месту возникновения затрат. Теги также можно использовать для упорядочивания затрат по среде выполнения, например сведения об использовании выставления счетов для виртуальных машин, запущенных в рабочей среде.

Сведения о тегах можно получить с помощью [интерфейсов API использования ресурсов Azure и RateCard](billing-usage-rate-card-overview.md) или файла сведений об использовании, разделенного запятыми (CSV-файла), который можно скачать на [портале учетных записей Azure](https://account.windowsazure.com/) или на [портале EA](https://ea.azure.com). Дополнительные сведения о программном доступе к данным для выставления счетов см. в разделе [Дополнительные сведения о потреблении ресурсов Microsoft Azure](billing-usage-rate-card-overview.md). Подробнее об операциях REST API см. в статье [Справочник по REST API выставления счетов Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

При скачивании файла сведений об использовании с разделителями запятыми для служб, поддерживающих теги выставления счетов, эти теги будут отображаться в столбце **Теги**. Дополнительные сведения см. в разделе [Расшифровка счета за использование Microsoft Azure](billing-understand-your-bill.md).

![См. сведения об использовании тегов при выставлении счетов.](./media/resource-group-using-tags/billing_csv.png)

## Дальнейшие действия

- Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. Для использования определяемой политики может потребоваться задать конкретный тег для всех ресурсов. Дополнительные сведения см. в статье [Применение политик для управления ресурсами и контроля доступа](resource-manager-policy.md).
- Общие сведения об использовании Azure PowerShell для развертывания ресурсов см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](./powershell-azure-resource-manager.md).
- Основные сведения об использовании Azure CLI для развертывания ресурсов см. в статье [Использование Azure CLI для Mac, Linux и Windows со службой управления ресурсами Azure](./xplat-cli-azure-resource-manager.md).
- Общие сведения об использовании портала см. в статье [Управление ресурсами Azure с помощью портала Azure](./azure-portal/resource-group-portal.md).  

<!---HONumber=AcomDC_0323_2016-->