<properties
   pageTitle="Развертывание ресурсов с помощью PowerShell и шаблона | Microsoft Azure"
   description="Узнайте, как использовать Azure Resource Manager и Azure PowerShell для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Интерфейс командной строки Azure](resource-group-template-deploy-cli.md)
- [Портал](resource-group-template-deploy-portal.md)
- [ИНТЕРФЕЙС REST API](resource-group-template-deploy-rest.md)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Узел](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)


В этом разделе объясняется, как использовать Azure PowerShell и шаблоны Resource Manager для развертывания ресурсов в Azure.

> [AZURE.TIP] Справку по отладке ошибок во время развертывания можно получить в следующих статьях.
>
> - [Просмотр операций развертывания с помощью Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) содержит информацию о том, как получить сведения, которые помогут устранить ошибку.
> - [Устранение распространенных ошибок при развертывании ресурсов в Azure с помощью диспетчера ресурсов Azure](resource-manager-common-deployment-errors.md) содержит информацию о том, как устранять распространенные ошибки при развертывании.

Шаблон может быть локальным файлом или внешним файл, доступным по универсальному коду ресурса (URI). Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS).

## Быстрое развертывание

В этой статье описываются все различные параметры, доступные при развертывании. Однако на практике вам чаще всего потребуются только две простые команды. Чтобы быстро приступить к выполнению развертывания, воспользуйтесь следующими командами:

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Дополнительные сведения о параметрах развертывания, которые могут быть более подходящими для вашего сценария, см. в последующих разделах этой статьи.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## Развертывание с помощью PowerShell

1. Войдите в свою учетную запись Azure.

        Add-AzureRmAccount

     Возвращается сводка по учетной записи.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Если у вас несколько подписок, укажите идентификатор той из них, которую хотите использовать для развертывания, с помощью команды **Set-AzureRmContext**.

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. Обычно при развертывании нового шаблона необходимо создать новую группу ресурсов, в которой будут размещены ресурсы. Если у вас уже есть группа ресурсов для развертывания, то можно пропустить этот шаг и просто использовать ее.

     Чтобы создать новую группу ресурсов, укажите ее имя и расположение.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Появится сводка по новой группе ресурсов.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Перед выполнением развертывания можно проверить его параметры. С помощью командлета **Test-AzureRmResourceGroupDeployment** можно выявить проблемы перед созданием фактических ресурсов. В следующем примере показано, как проверить развертывание.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Чтобы создать новое развертывание для группы ресурсов, выполните командлет **New-AzureRmResourceGroupDeployment** и укажите необходимые параметры. Параметры будут включать в себя имя развертывания, имя группы ресурсов, путь или URL-адрес созданного шаблона и другие параметры, необходимые для вашего сценария. Если параметр **Mode** не указан, то используется значение по умолчанию **Incremental**. Чтобы выполнить полное развертывание, установите для параметра **Режим** значение **Полный**. Будьте внимательны при использовании полного режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.

     Для развертывания локального шаблона используйте параметр **TemplateFile**.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Для развертывания внешнего шаблона используйте параметр **TemplateUri**.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Для предоставления значений параметров доступны следующие способы:
   
     1. Использование встроенных параметров.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Использование объекта параметра.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Использование локального файла параметров. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Использование внешнего файла параметров. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

     После развертывания ресурсов вы увидите сводку по развертыванию.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Если шаблон содержит параметр с именем, которое совпадает с одним из параметров в команде для развертывания шаблона (например, в шаблоне есть параметр с именем **ResourceGroupName**, которое совпадает с именем параметра **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), вам будет предложено указать название для параметра с постфиксом **FromTemplate** (например, **ResourceGroupNameFromTemplate**). В общем случае следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

6. Если вы хотите добавлять в журнал дополнительные сведения о развертывании, которые могут помочь в устранении ошибок развертывания, используйте параметр **DeploymentDebugLogLevel**. Можно задать регистрацию в журнале содержимого запроса или содержимого ответа (или и того, и другого) при операции развертывания.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Дополнительные сведения об отладке содержимого для устранения неполадок развертывания см. в разделе [Устранение неполадок развертываний групп ресурсов с помощью Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## Развертывание шаблона из хранилища с помощью маркера SAS

Шаблоны можно добавить в учетную запись хранения и создать ссылки на них во время развертывания с помощью маркера SAS.

> [AZURE.IMPORTANT] Если выполнить приведенные ниже действия, то большой двоичный объект, содержащий шаблон, будет доступен только владельцу учетной записи. Тем не менее, если создать маркер SAS для этого большого двоичного объекта, то он будет доступен любому пользователю, обладающему этим универсальным кодом ресурса (URI). Если другой пользователь перехватит этот универсальный код ресурса (URI), то сможет получить доступ к шаблону. Применение маркера SAS — хороший способ ограничить доступ к своим шаблонам, но не следует указывать конфиденциальные данные, например пароли, непосредственно в шаблоне.

### Добавление частного шаблона в учетную запись хранения

Ниже приведены действия по настройке учетной записи хранения для шаблонов.

1. Создайте новую группу ресурсов.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Создайте учетную запись хранения. Имя учетной записи хранения должно быть уникальным в Azure, поэтому введите собственное имя для учетной записи.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Задайте текущую учетную запись хранения.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Создайте новый контейнер. Разрешение имеет значение **Off**; это означает, что контейнер доступен только владельцу.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Добавьте свой шаблон в контейнер.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### Предоставление маркера SAS во время развертывания

Чтобы развернуть частный шаблон в учетной записи хранения, извлеките маркер SAS и добавьте его в универсальный код ресурса (URI) для шаблона.

1. Если вы изменили текущую учетную запись хранения, выберите текущей учетную запись хранения, содержащую ваши шаблоны.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Создайте маркер SAS с разрешениями на чтение и сроком действия, чтобы ограничить доступ. Извлеките полный универсальный код ресурса (URI) шаблона с маркером SAS.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Разверните шаблон, указав универсальный код ресурса (URI), который включает в себя маркер SAS.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Пример использования маркера SAS со связанными шаблонами см. в разделе [Использование связанных шаблонов в диспетчере ресурсов Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## Дальнейшие действия
- Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание ресурсов с использованием библиотек .NET и шаблона](virtual-machines/virtual-machines-windows-csharp-template.md).
- Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
- Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](solution-dev-test-environments.md).
- Дополнительные сведения об использовании ссылки на KeyVault для передачи безопасных значений см. в разделе [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0713_2016-->