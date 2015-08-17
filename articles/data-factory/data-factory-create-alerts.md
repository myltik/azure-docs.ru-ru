<properties 
	pageTitle="Создание оповещений о событиях фабрики данных Azure" 
	description="Узнайте, как создавать оповещения в событиях, вызываемых Azure для операций фабрики данных." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Создание оповещений в событиях Azure
События Azure позволяют получить представление о том, что происходит в ресурсах Azure. Azure регистрирует пользовательские события, когда ресурс Azure (например, фабрика данных) создается, обновляется или удаляется. При использовании фабрики данных Azure события создаются, когда:
 
1.	создается, обновляется или удаляется фабрика данных Azure;
2.	запускается или завершается обработка данных (которая называется циклом выполнения);
3.	создается и удаляется кластер HDInsight по требованию.

Вы можете создавать оповещения в этих пользовательских событиях и настраивать их для отправки уведомлений по электронной почте администраторам и соадминистраторам подписки. Кроме того, вы можете указать дополнительные адреса электронной почты пользователей, которые должны получать уведомления по электронной почте при выполнении определенных условий.

## Задание определения оповещения
Чтобы задать определение оповещения, создайте файл JSON, описывающий операции, о которых вы хотите получать оповещения. В следующем примере оповещение будет отправлять уведомление по электронной почте для операции **RunFinished**. Говоря точнее, уведомление по электронной почте отправляется после завершения выполнения в фабрике данных со сбоем (состояние = FailedExecution).

	{
    	"contentVersion": "1.0.0.0",
   		 "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    	"parameters": {},
    	"resources": 
		[
        	{
            	"name": "ADFAlertsSlice",
            	"type": "microsoft.insights/alertrules",
            	"apiVersion": "2014-04-01",
            	"location": "East US",
            	"properties": 
				{
                	"name": "ADFAlertsSlice",
                	"description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                	"isEnabled": true,
                	"condition": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    	"dataSource": 
						{
                        	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        	"operationName": "RunFinished",
			         		"status": "Failed",
                        		"subStatus": "FailedExecution"   
                    	}
                	},
                	"action": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    	"customEmails": [ "<your alias>@contoso.com" ]
                	}
            	}
        	}
    	]
	}

В приведенном выше определении JSON **subStatus** можно удалить, если вы не хотите получать оповещение при конкретном сбое.

Список операций и состояний (а также подсостояний) см. в разделе [Доступные операции и состояния](#AvailableOperationsStatuses).

## Развертывание оповещения
Для развертывания оповещения используйте командлет Azure PowerShell **New-AzureResourceGroupDeployment**, как показано в следующем примере:

	New-AzureResourceGroupDeployment -ResourceGroupName adf 	-TemplateFile .\ADFAlertFailedSlice.json -StorageAccountName testmetricsabc

Параметр StorageAccountName задает учетную запись хранения для сохранения файла JSON развернутого предупреждения.

После успешного завершения развертывания группы ресурсов вы увидите следующие сообщения:
	
	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## Получение списка развертываний групп ресурсов Azure
Чтобы получить список развертываний групп ресурсов Azure, используйте командлет **Get-AzureResourceGroupDeployment**, как показано в следующем примере:
	
	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## <a name="AvailableOperationsStatuses"></a>Доступные имена операций и значения состояний

| Имя операции | Состояние | Подсостояние |
| -------------- | ------ | ---------- |
| RunStarted | Started | Starting |
| RunFinished | Сбой/успешное завершение |	<p>FailedResourceAllocation </p><p>Succeeded</p><p>FailedExecution</p><p>TimedOut</p><p>Canceled</p><p>FailedValidation</p><p>Abandoned</p> | 
| SliceOnTime | In Progress | Ontime |
| SliceDelayed | In Progress | Late |
| OnDemandClusterCreateStarted | Started | |
| OnDemandClusterCreateSuccessful | Succeeded | | 
| OnDemandClusterDeleted | Succeeded | |


## Устранение неполадок пользовательских событий
Выполните следующую команду для просмотра созданных событий:

	Get-AzureResourceGroupLog –Name $ResourceGroup -All | Where-Object EventSource -eq "Microsoft.DataFactory"
 

<!---HONumber=August15_HO6-->