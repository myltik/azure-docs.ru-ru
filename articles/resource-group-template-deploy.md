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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Интерфейс командной строки Azure](resource-group-template-deploy-cli.md)
- [Портал](resource-group-template-deploy-portal.md)
- [ИНТЕРФЕЙС REST API](resource-group-template-deploy-rest.md)

В этом разделе объясняется, как использовать Azure PowerShell с шаблонами Resource Manager для развертывания ресурсов в Azure.

> [AZURE.TIP] Справку по отладке ошибок во время развертывания можно получить в следующих статьях.
>
> - [Просмотр операций развертывания с помощью Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) — руководство по получению сведений, которые помогут устранить ошибку.
> - [Устранение распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md) — руководство по устранению распространенных ошибок при развертывании.

Шаблон может быть локальным файлом или внешним файл, доступным по универсальному коду ресурса (URI). Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS).

## Быстрое развертывание

В этой статье описаны все доступные во время развертывания параметры. Но обычно достаточно двух простых команд. Чтобы быстро приступить к выполнению развертывания, воспользуйтесь следующими командами:

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

2. Если у вас несколько подписок, укажите идентификатор той, которая будет использоваться для развертывания, с помощью команды **Set-AzureRmContext**.

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. Как правило, при развертывании нового шаблона для размещения ресурсов создается группа ресурсов. Если у вас уже есть группа ресурсов, в которую можно развернуть шаблон, пропустите этот шаг и используйте существующую группу.

     Чтобы создать группу ресурсов, укажите ее имя и расположение. Вам нужно указать расположение группы, так как она хранит метаданные ресурсов. Для соответствия требованиям вам, возможно, нужно указать, где хранятся эти метаданные. Обычно мы рекомендуем указывать расположение, в котором будет храниться большинство ресурсов. Используя одно и то же расположение, можно упростить шаблон.

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

4. Перед выполнением развертывания можно проверить соответствующие параметры. С помощью командлета **Test-AzureRmResourceGroupDeployment** можно выявить проблемы до создания фактических ресурсов. В следующем примере показано, как проверить развертывание.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Чтобы развернуть ресурсы в группе ресурсов, выполните командлет **New-AzureRmResourceGroupDeployment** и укажите необходимые параметры: имя развертывания, имя группы ресурсов, путь или URL-адрес созданного шаблона, а также другие необходимые вам параметры. Если параметр **Режим** не задан, используется стандартное значение **Добавочный**. Чтобы выполнить полное развертывание, установите для параметра **Режим** значение **Полный**. Будьте внимательны при использовании полного режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.

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

        При использовании внешнего файла параметров нельзя передавать другие значения в командной строке или локальном файле. Дополнительные сведения см. в разделе [Приоритет параметров](#parameter-precendence).

     После развертывания ресурсов вы увидите сводку по развертыванию.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Если шаблон содержит параметр, имя которого совпадает с именем одного из параметров в команде PowerShell, появится окно с запросом указать значение для этого параметра. Параметр из шаблона должен включать постфикс **FromTemplate**. Предположим, что параметр с именем **ResourceGroupName** в шаблоне конфликтует с параметром **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx). Вам будет предложено указать значение для параметра **ResourceGroupNameFromTemplate**. В общем случае следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

6. Если вы хотите включить в журнал дополнительные сведения о развертывании, которые могут помочь в устранении ошибок развертывания, используйте параметр **DeploymentDebugLogLevel**. Можно задать регистрацию в журнале содержимого запроса или содержимого ответа (или и того, и другого) при операции развертывания.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Дополнительные сведения об отладке содержимого для устранения неполадок при развертывании см. в статье [Просмотр операций развертывания с помощью Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## Развертывание шаблона из хранилища с помощью маркера SAS

Шаблоны можно добавить в учетную запись хранения и создать ссылки на них во время развертывания с помощью маркера SAS.

> [AZURE.IMPORTANT] Если выполнить приведенные ниже действия, то большой двоичный объект, содержащий шаблон, будет доступен только владельцу учетной записи. Тем не менее, если создать маркер SAS для этого большого двоичного объекта, то он будет доступен любому пользователю, обладающему этим универсальным кодом ресурса (URI). Если другой пользователь перехватит этот универсальный код ресурса (URI), то сможет получить доступ к шаблону. Применение маркера SAS — хороший способ ограничить доступ к своим шаблонам, но не следует указывать конфиденциальные данные, например пароли, непосредственно в шаблоне.

### Добавление частного шаблона в учетную запись хранения

Ниже приведены действия по настройке учетной записи хранения для шаблонов.

1. Создайте группу ресурсов.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Создайте учетную запись хранения. Имя учетной записи хранения должно быть уникальным в Azure, поэтому введите собственное имя для учетной записи.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Задайте текущую учетную запись хранения.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Создайте контейнер. Разрешение имеет значение **Off**, это означает, что контейнер доступен только владельцу.

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

Пример использования маркера SAS со связанными шаблонами см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## Приоритет параметров

Вы можете использовать в ходе одной операции развертывания как встроенные параметры, так и локальный файл параметров. Например, часть значений можно указать в локальном файле параметров, а другую часть — в команде развертывания. Если значения для одного параметра указаны одновременно и в локальном файле параметров, и в командной строке, более высокий приоритет имеет значение из командной строки.

При этом параметры нельзя указывать в командной строке, если используется внешний файл параметров. Если в параметре **TemplateParameterUri** будет указан файл параметров, все параметры командной строки игнорируются. В таком случае все значения параметров нужно указывать только во внешнем файле. Если в шаблоне используются конфиденциальные значения, которые нельзя включать в файл параметров, есть два варианта. Такие значения можно передать в хранилище ключей, создав соответствующую ссылку во внешнем файле параметров, или же можно динамически указать значения всех параметров в командной строке.

Дополнительные сведения об использовании ссылки на хранилище ключей для безопасной передачи значений см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md).

## Дальнейшие действия
- Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](virtual-machines/virtual-machines-windows-csharp-template.md).
- Сведения об определении параметров в шаблоне см. в разделе [Параметры](resource-group-authoring-templates.md#parameters).
- Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](solution-dev-test-environments.md).

<!---HONumber=AcomDC_0921_2016-->