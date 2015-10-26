<properties
   pageTitle="Развертывание ресурсов с помощью шаблона диспетчера ресурсов | Microsoft Azure"
   services="azure-resource-manager"
   description="Используйте диспетчер ресурсов Azure для развертывания ресурсов в Azure. Шаблон — это JSON-файл, который можно использовать с помощью портала, PowerShell, интерфейса командной строки Azure для Mac, Linux и Windows или REST."
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="tomfitz"/>

# Развертывание приложения с использованием шаблона диспетчера ресурсов Azure

В этом разделе объясняется, как использовать шаблоны диспетчера ресурсов Azure для развертывания приложения в Azure. Здесь описаны способы развертывания приложения с помощью Azure PowerShell, интерфейса командной строки Azure, REST API и портала предварительной версии Microsoft Azure.

Основные сведения о диспетчере ресурсов см. в статье [Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md). Сведения о создании шаблонов см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

При развертывании приложения с помощью шаблона можно предоставить значения параметров для настройки способа создания ресурсов. Значения для этих параметров можно указать в процессе или в файле параметров.


## Развертывание с помощью PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]


1. Войдите в свою учетную запись Azure. После предоставления учетных данных команда возвращает информацию о вашей учетной записи.

    До предварительной версии Azure PowerShell 1.0:

        PS C:\> Switch-AzureMode AzureResourceManager
        ...
        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

    Предварительная версия Azure PowerShell 1.0:

         PS C:\> Login-AzureRmAccount

         Evironment : AzureCloud
         Account    : someone@example.com
         ...


2. Если у вас несколько подписок, укажите идентификатор подписки, которую хотите использовать для развертывания, с помощью команды **Select-AzureRmSubscription**.

        PS C:\> Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

3. Если у вас нет существующей группы ресурсов, создайте группу ресурсов с помощью команды **New-AzureRmResourceGroup**. Введите имя группы ресурсов и расположение, необходимое для решения. Появится сводка по новой группе ресурсов.

        PS C:\> New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Чтобы создать новое развертывание для группы ресурсов, выполните команду **New-AzureRmResourceGroupDeployment** и укажите необходимые параметры. Параметры будут включать в себя имя развертывания, имя группы ресурсов, путь или URL-адрес созданного шаблона и другие параметры, необходимые для вашего сценария.
   
     Для предоставления значений параметров доступны следующие способы:
   
     - Использование встроенных параметров.

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - Использование объекта параметра.

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - Использование файла параметров. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](./#parameter-file).

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     После развертывания группы ресурсов вы увидите сводку по развертыванию.

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. Вот как можно получить информацию о сбоях развертывания.

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -Name ExampleDeployment

        
### Видео

Вот видео, в котором демонстрируется работа с шаблонами диспетчера ресурсов с помощью PowerShell.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Развертывание с помощью интерфейса командной строки Azure для Mac, Linux и Windows

Информацию об использовании интерфейса командной строки Azure с диспетчером ресурсов см. в статье [Использование интерфейса командной строки Azure для Mac, Linux и Windows в режиме управления ресурсами Azure](../xplat-cli-azure-resource-manager.md).

1. Войдите в свою учетную запись Azure. После предоставления учетных данных команда возвращает результат вашего входа.

        azure login
  
        ...
        info:    login command OK

2. Если у вас несколько подписок, укажите идентификатор подписки, которую вы хотите использовать для развертывания.

        azure account set <YourSubscriptionNameOrId>

3. Переключитесь на модуль диспетчера ресурсов Azure. Вы получите подтверждение нового режима.

        azure config mode arm
   
        info:     New mode is arm

4. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Введите имя группы ресурсов и расположение, необходимое для решения. Появится сводка по новой группе ресурсов.

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Чтобы создать новое развертывание для группы ресурсов, выполните следующую команду и укажите необходимые параметры. Параметры будут включать в себя имя развертывания, имя группы ресурсов, путь или URL-адрес созданного шаблона и другие параметры, необходимые для вашего сценария.
   
     Для предоставления значений параметров доступны следующие способы:

     - Использование встроенных параметров и локального шаблона. Каждый параметр имеет следующий формат: `"ParameterName": { "value": "ParameterValue" }`. В приведенном ниже примере показаны параметры с escape-символами.

             azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - Использование встроенных параметров и ссылки на шаблон.

             azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - Использование файла параметров. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](./#parameter-file).
    
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     После развертывания группы ресурсов вы увидите сводку по развертыванию.
  
           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. Вот как можно получить информацию о последнем развертывании.

         azure group log show -l ExampleResourceGroup

7. Вот как можно получить подробную информацию о сбоях развертывания.
      
         azure group log show -l -v ExampleResourceGroup

## Развертывание с помощью REST API
1. Задайте [общие параметры и заголовки](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), включая маркеры аутентификации.
2. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Укажите идентификатор группы ресурсов, имя новой группы ресурсов и расположение, необходимое для решения. Дополнительную информацию см. в разделе [Создание группы ресурсов](https://msdn.microsoft.com/library/azure/dn790525.aspx).

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
           <common headers>
           {
             "location": "West US",
             "tags": {
               "tagname1": "tagvalue1"
             }
           }
   
3. Создайте развертывание новой группы ресурсов. Укажите идентификатор подписки, имя группы ресурсов для развертывания, имя развертывания и расположение шаблона. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](./#parameter-file). Дополнительную информацию о REST API для создания группы ресурсов см. в разделе [Создание шаблона-развертывания](https://msdn.microsoft.com/library/azure/dn790564.aspx).
    
         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
            <common headers>
            {
              "properties": {
                "templateLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                  "contentVersion": "1.0.0.0",
                },
                "mode": "Incremental",
                "parametersLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                  "contentVersion": "1.0.0.0",
                }
              }
            }
   
4. Получите состояние развертывания шаблона. Дополнительную информацию см. в разделе [Получение сведений о шаблоне-развертывания](https://msdn.microsoft.com/library/azure/dn790565.aspx).

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## Развертывание с помощью портала предварительной версии

Представляете, Каждое приложение, созданное с помощью [портала предварительной версии](https://portal.azure.com/), реализуется на базе шаблона диспетчера ресурсов Azure! Просто создавая на портале виртуальную машину, виртуальную сеть, учетную запись хранения, службу приложений или базу данных, вы уже используете преимущества диспетчера ресурсов Azure без лишних усилий. Просто выберите значок **Создать** и начните развертывать приложение, используя диспетчер ресурсов Azure.

![Создать](./media/resource-group-template-deploy/new.png)

Дополнительные сведения об использовании портала с диспетчером ресурсов Azure см. в статье [Управление ресурсами Azure с помощью портала предварительной версии Azure](resource-group-portal.md).


## Файл параметров

Если вы используете файл параметров для передачи значений параметров в шаблон во время развертывания, необходимо создать JSON-файл с форматом, как в следующем примере.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            }
       }
    }

Размер файла параметров не может быть более 64 КБ.

## Дальнейшие действия
- Пример развертывания ресурсов через клиентскую библиотеку .NET см. в статье [Развертывание ресурсов с использованием библиотек .NET и шаблона](../arm-template-deployment.md).
- Подробный пример развертывания приложения см. в статье [Предсказуемые подготовка и развертывание микрослужб в Azure.](../app-service-web/app-service-deploy-complex-application-predictably.md)
- Инструкции по развертыванию вашего решения для различных сред см. в статье [Среды разработки и тестирования в Microsoft Azure](../solution-dev-test-environments-preview-portal.md).
- Дополнительную информацию о разделах в шаблоне диспетчера ресурсов Azure см. в статье [Создание шаблонов](../resource-group-authoring-templates.md).
- Список функций, которые можно использовать в шаблоне диспетчера ресурсов Azure, см. в статье [Функции шаблонов](../resource-group-template-functions.md).

 

<!---HONumber=Oct15_HO3-->