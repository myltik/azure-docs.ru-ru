<properties
   pageTitle="Развертывание приложения с использованием шаблона диспетчера ресурсов Azure"
   services="azure-resource-manager"
   description="Развертывание приложения в Azure с использованием диспетчера ресурсов Azure. Шаблон — это JSON-файл, который можно использовать с помощью портала, PowerShell, интерфейса командной строки Azure для Mac, Linux и Windows или REST."
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
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Развертывание приложения с использованием шаблона диспетчера ресурсов Azure

В этом разделе объясняется, как использовать шаблоны диспетчера ресурсов Azure для развертывания приложения в Azure. Здесь описаны способы развертывания приложения с помощью Azure PowerShell, интерфейса командной строки Azure, REST API и портала предварительной версии Microsoft Azure.

Шаблоны диспетчера ресурсов Azure позволяют быстро и легко подготовить приложения в Azure с помощью декларативного JSON-файла. В одном шаблоне JSON можно развернуть несколько служб, таких как виртуальные машины, виртуальные сети, хранилища, службы приложений и базы данных. Для развертывания приложения на каждом этапе его жизненного цикла используется один шаблон.

Чтобы упростить управление приложением, можно упорядочить все ресурсы с общим жизненным циклом в одну группу ресурсов. Группы ресурсов позволяют вместе развертывать, обновлять и удалять все связанные ресурсы. В большинстве случаев группа ресурсов сопоставляется с одним приложением или уровнем приложений (для объемных приложений). Ресурсы, развернутые с помощью шаблона, будут находиться в одной группе ресурсов, но они могут включать в себя зависимости из других групп ресурсов.

В группе ресурсов можно отслеживать выполнение развертывания, а также видеть его состояние и все выходные данные, связанные с выполнением шаблона.

При развертывании приложения с помощью шаблона можно предоставить значения параметров для настройки способа создания ресурсов. Значения для этих параметров можно указать в процессе или в файле параметров.

## Основные понятия

- Группа ресурсов — коллекция сущностей с общим жизненным циклом.
- Шаблон диспетчера ресурсов — декларативный JSON-файл, определяющий целевое состояние развертывания.
- Развертывание — операция, которая отслеживает выполнение шаблона диспетчера ресурсов.
- Параметры — значения, которые предоставляет пользователь, выполняющий развертывание для настройки развернутых ресурсов.
- Файл параметров — JSON-файл, который содержит имена и значения параметров. 

## Сценарии

Шаблоны диспетчера ресурсов позволяют выполнять следующие действия.

- Развертывание сложных многоуровневых приложений, таких как Microsoft SharePoint.
- Последовательное и повторное развертывание приложений.
- Поддержка рабочей среды, а также сред разработки и тестирования.
- Просмотр состояния развертываний.
- Устранение неполадок развертывания с использованием журналов аудита развертывания.

## Развертывание с помощью портала предварительной версии

Представляете, каждое приложение в коллекции реализуется на базе шаблона диспетчера ресурсов Azure! Просто создавая на портале виртуальную машину, виртуальную сеть, учетную запись хранения, службу приложений или базу данных, вы уже используете преимущества диспетчера ресурсов Azure без лишних усилий.

Чтобы устранить неполадки развертывания с помощью портала предварительного просмотра, щелкните **Обзор** -> **Группы ресурсов** -> *имя вашей группы ресурсов*. Здесь же щелкните плитку **События** в области **Мониторинг**. Вы можете выбрать отдельную **операцию** и **событие**, чтобы просмотреть дополнительную информацию.

## Развертывание с помощью PowerShell

Информацию об использовании Azure PowerShell с диспетчером ресурсов см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).

1. Войдите в свою учетную запись Azure. После предоставления учетных данных команда возвращает информацию о вашей учетной записи.

        PS C:> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. Если у вас несколько подписок, укажите идентификатор подписки, которую вы хотите использовать для развертывания.

        PS C:> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Переключитесь на модуль диспетчера ресурсов Azure.

        PS C:> Switch-AzureMode AzureResourceManager

4. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Введите имя группы ресурсов и расположение, необходимое для решения. Появится сводка по новой группе ресурсов.

        PS C:> New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Чтобы создать новое развертывание для группы ресурсов, выполните команду **New-AzureResourceGroupDeployment** и укажите необходимые параметры. Параметры будут включать в себя имя развертывания, имя группы ресурсов, путь или URL-адрес созданного шаблона и другие параметры, необходимые для вашего сценария.
   
     Для предоставления значений параметров доступны следующие способы:
   
     - Использование встроенных параметров.

            PS C:> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - Использование объекта параметра.

            PS C:> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - Использование файла параметров. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](./#parameter-file).

            PS C:> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     После развертывания группы ресурсов вы увидите сводку по развертыванию.

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. Вот как можно получить информацию о сбоях развертывания.

        PS C:> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Вот как можно получить подробную информацию о сбоях развертывания.

        PS C:> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput

## Развертывание с помощью интерфейса командной строки Azure для Mac, Linux и Windows

Информацию об использовании интерфейса командной строки Azure с диспетчером ресурсов см. в статье [Использование интерфейса командной строки Azure для Mac, Linux и Windows в режиме управления ресурсами Azure](../xplat-cli-azure-resource-manager.md)￼.

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

     - Использование встроенных параметров и локального шаблона.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - Использование встроенных параметров и ссылки на шаблон.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

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

## Дальнейшие действия
- [Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md)
- [Развертывание ресурсов с использованием библиотек .NET и шаблона](../arm-template-deployment.md)
- [Предсказуемое развертывание сложного приложения в Azure](../app-service-web/app-service-deploy-complex-application-predictably.md)
- [Создание шаблонов](../resource-group-authoring-templates.md)
- [Функции шаблонов](../resource-group-template-functions.md)
- [Расширенные операции с шаблонами](../resource-group-advanced-template.md)  

 

<!---HONumber=July15_HO3-->