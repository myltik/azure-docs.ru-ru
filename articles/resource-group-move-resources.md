<properties 
	pageTitle="Перемещение ресурсов в новую группу ресурсов" 
	description="Перемещайте ресурсы в новую группу ресурсов для диспетчера ресурсов Azure с помощью Azure PowerShell или REST API." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="tomfitz"/>

# Перемещение ресурсов в новую группу ресурсов или подписку

В этом разделе показано, как перемещать ресурсы из одной группы ресурсов в другую. Можно также перемещать ресурсы в новую подписку. Перемещение ресурсов может потребоваться в следующих случаях:

1. Для выставления счетов ресурс должен находиться в другой подписке.
2. Теперь жизненные циклы этого ресурса и ресурсов, с которыми он ранее был сгруппирован, не совпадают. Ресурс нужно переместить в новую группу ресурсов, чтобы управлять им отдельно от других ресурсов.
3. Теперь жизненные циклы ресурса и остальных ресурсов в другой группе ресурсов совпадают. Ресурс нужно переместить в группу ресурсов с другими ресурсами, чтобы управлять ими вместе.

Вот некоторые важные особенности, которые следует учитывать при перемещении ресурса:

1. Расположение ресурса изменить невозможно. При перемещении ресурс всего лишь перемещается в новую группу ресурсов. Даже если новая группа ресурсов находится в другом расположении, расположение ресурса не меняется.
2. Группа ресурсов назначения должна содержать только те ресурсы, жизненные циклы приложений которых совпадают с аналогичными жизненными циклами перемещаемых ресурсов.
3. Если вы используете Azure PowerShell, убедитесь, что у вас установлена последняя версия. Команда **Move-AzureResource** часто обновляется. Чтобы обновить текущую версию, запустите установщик веб-платформы Майкрософт и проверьте доступность более новой версии. Дополнительную информацию см. в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md).
4. Операция перемещения может занять некоторое время. В это время командная строка PowerShell будет ожидать ее завершения.

## Поддерживаемые службы

В настоящее время в некоторых службах перемещение ресурсов не поддерживается.

Сейчас к службам, которые поддерживают перемещение в новую группу ресурсов и подписку, относятся:

- Управление API
- поиск Azure;
- Фабрика данных
- хранилище ключей;
- Mobile Engagement;
- Operational Insights;
- Кэш Redis

Службы с поддержкой перемещения в новую группу ресурсов, но не в новую подписку:

- служба вычислений (классическая);
- служба хранилища (классическая).

Службы, которые в настоящее время не поддерживают перемещение ресурсов:

- Виртуальные сети

## Перемещение ресурсов с помощью PowerShell

Чтобы переместить существующие ресурсы в другую группу ресурсов или подписку, используйте команду **Move-AzureResource**.

В первом примере показано, как переместить один ресурс в новую группу ресурсов.

    PS C:\> Move-AzureResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

Во втором примере показано, как переместить несколько ресурсов в новую группу ресурсов.

    PS C:\> $webapp = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExampleSite -ResourceType Microsoft.Web/sites
    PS C:\> $plan = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExamplePlan -ResourceType Microsoft.Web/serverFarms
    PS C:\> Move-AzureResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

Чтобы переместить ресурс в новую подписку, добавьте значение для параметра **DestinationSubscriptionId**.

## Перемещение ресурсов с помощью REST API

Чтобы переместить существующие ресурсы в другую группу ресурсов или подписку, выполните следующую команду:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Замените **{source-subscription-id}** и **{source-resource-group-name}** подпиской и группой ресурсов, содержащими ресурсы, которые нужно переместить. Используйте **2015-01-01** для {api-version}.

В запросе включите объект JSON, который определяет целевую группу ресурсов и ресурсы, которые нужно переместить.

    {
        "targetResourceGroup": "/subscriptions/{target-subscription-id}/resourceGroups/{target-resource-group-name}", "resources": [
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}"
        ]
    }

## Дальнейшие действия
- [Использование Azure PowerShell с диспетчером ресурсов](./powershell-azure-resource-manager.md)
- [Использование интерфейса командной строки Azure с диспетчером ресурсов](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Управление ресурсами с помощью портала Azure](azure-portal/resource-group-portal.md)
- [Использование тегов для организации ресурсов](./resource-group-using-tags.md)

<!---HONumber=July15_HO4-->