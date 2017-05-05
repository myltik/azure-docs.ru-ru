---
title: "Создание ресурсов служебной шины Azure с использованием шаблонов Azure Resource Manager | Документация Майкрософт"
description: "С помощью шаблонов Azure Resource Manager можно автоматизировать создание ресурсов служебной шины."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/18/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 297c01ec31b584b354fbd751a9c3c86a379ac63e
ms.lasthandoff: 04/21/2017


---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Создание ресурсов служебной шины с использованием шаблонов Azure Resource Manager

В этой статье описывается, как создать и развернуть ресурсы служебной шины с помощью шаблонов Azure Resource Manager, PowerShell и поставщика ресурсов служебной шины.

В шаблонах Azure Resource Manager определяются ресурсы для развертывания решения и указываются параметры и переменные, позволяющие вводить значения для различных сред. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания. Дополнительные сведения о создании шаблонов Azure Resource Manager и описание формата шаблонов см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> В примерах в этой статье показано, как использовать Azure Resource Manager для создания пространства имен и объекта обмена сообщениями (очереди) служебной шины. Чтобы узнать о новых шаблонах, в [коллекции шаблонов быстрого запуска Azure][Azure Quickstart Templates gallery] выполните поиск по запросу "служебная шина".
>
>

## <a name="service-bus-resource-manager-templates"></a>Шаблоны Resource Manager для служебной шины

Эти шаблоны Azure Resource Manager для служебной шины доступны для скачивания и развертывания. Чтобы получить подробные сведения о каждом из них со ссылками на шаблоны в GitHub, щелкните приведенные ниже ссылки.

* [Создайте пространство имен служебной шины](service-bus-resource-manager-namespace.md)
* [Создание пространства имен служебной шины с очередью](service-bus-resource-manager-namespace-queue.md)
* [Создание пространства имен служебной шины с разделом и подпиской](service-bus-resource-manager-namespace-topic.md)
* [Создание пространства имен служебной шины с очередью и правилом авторизации](service-bus-resource-manager-namespace-auth-rule.md)
* [Создание пространства имен служебной шины с разделом, подпиской и правилом с помощью шаблона Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Развертывание с помощью PowerShell

Ниже описывается использование PowerShell для развертывания шаблона Azure Resource Manager, который создает пространство имен служебной шины уровня **Standard** и очередь в ней. Этот пример основан на шаблоне [Создание пространства имен служебной шины с очередью](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue). Ниже приведена примерная последовательность действий.

1. Установите PowerShell.
2. Создайте шаблон и (необязательно) файл параметров.
3. В PowerShell войдите в свою учетную запись Azure.
4. Создайте группу ресурсов, если ее еще нет.
5. Протестируйте развертывание.
6. Если необходимо, задайте режим развертывания.
7. Разверните шаблон.

Подробные сведения о развертывании шаблонов Azure Resource Manager см. в статье [Deploy resources with Resource Manager templates and Azure PowerShell][Deploy resources with Azure Resource Manager templates] (Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell).

### <a name="install-powershell"></a>Установка PowerShell

Установите Azure PowerShell, следуя указаниям в разделе [Getting started with Azure PowerShell](/powershell/azure/get-started-azureps) (Приступая к работе с Azure PowerShell).

### <a name="create-a-template"></a>Создание шаблона

Клонируйте и скопируйте шаблон [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) из GitHub.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>Создание файла параметров (необязательно)

Чтобы использовать необязательный файл параметров, скопируйте файл [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json). Замените значение `serviceBusNamespaceName` на название пространства имен служебной шины, которое нужно создать в этом развертывании, а значение `serviceBusQueueName` — на имя очереди, которую нужно создать.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Дополнительные сведения см. в разделе [Параметры](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Вход в Azure и настройка подписки Azure

В командной строке PowerShell выполните следующую команду:

```powershell
Login-AzureRmAccount
```

Вам будет предложено войти в учетную запись Azure. Войдя в систему, выполните следующую команду, чтобы просмотреть доступные подписки.

```powershell
Get-AzureRMSubscription
```

Эта команда возвращает список доступных подписок Azure. Выберите подписку для текущего сеанса, выполнив приведенную ниже команду. Замените `<YourSubscriptionId>` идентификатором GUID подписки Azure, которую вы хотите использовать.

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Задание группы ресурсов

Если у вас нет группы ресурсов, создайте ее с помощью команды **New-AzureRmResourceGroup**. Введите имя группы ресурсов и нужное расположение. Например:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

После успешного выполнения операции появится сводка по новой группе ресурсов.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>тестирование развертывания

Проверьте развертывание, выполнив командлет `Test-AzureRmResourceGroupDeployment`. При тестировании развернутой службы укажите точно такие же параметры, как и при ее выполнении.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Создание развертывания

Чтобы создать развертывание, выполните командлет `New-AzureRmResourceGroupDeployment` и укажите необходимые параметры при появлении запроса. Параметры включают в себя имя развертывания, имя группы ресурсов и путь к файлу шаблона или его URL-адрес. Если параметр **Режим** не указан, используется стандартное значение **Добавочный**. Дополнительные сведения см. в статье [Добавочные и полные развертывания](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

Следующая команда запрашивает три параметра в окне PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Чтобы использовать вместо этого файл параметров, выполните приведенную ниже команду.

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

При выполнении командлета развертывания также можно использовать встроенные параметры. Команда выглядит следующим образом:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Чтобы выполнить [полное](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) развертывание, установите для параметра **Режим** значение **Полный**.

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Проверка развертывания
В случае успешного развертывания ресурсов в окне PowerShell будет приведена сводка по развертыванию.

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>Дальнейшие действия
Мы рассмотрели базовую процедуру и команды для развертывания шаблона Azure Resource Manager. Для получения более подробных сведений перейдите по следующим ссылкам:

* [Общие сведения о диспетчере ресурсов Azure][Azure Resource Manager overview]
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Создание шаблонов диспетчера ресурсов Azure](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus

