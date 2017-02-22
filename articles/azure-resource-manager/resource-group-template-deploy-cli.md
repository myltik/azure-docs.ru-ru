---
title: "Развертывание ресурсов с помощью интерфейса командной строки Azure и шаблона | Документация Майкрософт"
description: "Узнайте, как использовать Azure Resource Manager и Azure CLI для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4851e872349fa6483e1f1a340d0968e845a3518
ms.openlocfilehash: ccbb918a3377094395a27a9b7a63f213c5085027


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Интерфейс командной строки Azure](resource-group-template-deploy-cli.md)
> * [Портал](resource-group-template-deploy-portal.md)
> * [ИНТЕРФЕЙС REST API](resource-group-template-deploy-rest.md)
> 
> 

В этой статье объясняется, как использовать интерфейс командной строки Azure и шаблоны Resource Manager для развертывания ресурсов в Azure.  Шаблон может быть локальным файлом или внешним файл, доступным по универсальному коду ресурса (URI). Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS).

> [!TIP]
> Справку по отладке ошибок во время развертывания можно получить в следующих статьях.
> 
> * Статья [Просмотр операций развертывания с помощью Azure Resource Manager](resource-manager-deployment-operations.md) содержит информацию о том, как получить сведения, которые помогут устранить ошибку.
> * [Устранение распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md) — руководство по устранению распространенных ошибок при развертывании.
> 
> 

## <a name="quick-steps-to-deployment"></a>Быстрое развертывание
Чтобы быстро приступить к выполнению развертывания, воспользуйтесь следующими командами:

```
azure group create -n examplegroup -l "West US"
azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g examplegroup -n exampledeployment
```

Эти команды создают группу ресурсов и развертывают в ней шаблон. Файл шаблона и файл параметров являются локальными. Если это вам подходит, то все необходимое готово к развертыванию ресурсов. Тем не менее, существуют дополнительные параметры, с помощью которых можно указать развертываемые ресурсы. В оставшейся части этой статьи описаны все доступные во время развертывания параметры. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>Развернуть
Информацию об использовании интерфейса командной строки Azure с Resource Manager см. в статье [Use the Azure CLI to manage Azure resources and resource groups](xplat-cli-azure-resource-manager.md) (Использование интерфейса командной строки Azure для управления ресурсами и группами ресурсов Azure).

1. Войдите в свою учетную запись Azure. После предоставления учетных данных команда возвращает результат вашего входа.
   
   ```
   azure login
   ```
2. Если у вас несколько подписок, укажите подписку, которую вы хотите использовать для развертывания.
   
   ```
   azure account set <YourSubscriptionNameOrId>
   ```
4. При развертывании шаблона необходимо указать группу ресурсов, которая будет содержать развернутые ресурсы. Если у вас уже есть группа ресурсов, в которую можно развернуть шаблон, пропустите этот шаг и используйте существующую группу. 
   
     Чтобы создать группу ресурсов, укажите ее имя и расположение. Следует указать расположение группы ресурсов, так как она хранит метаданные ресурсов. Для соответствия требованиям вам, возможно, нужно указать, где хранятся эти метаданные. Обычно мы рекомендуем указывать расположение, в котором будет храниться большинство ресурсов. Используя одно и то же расположение, можно упростить шаблон.
   
   ```
   azure group create -n ExampleResourceGroup -l "West US"
   ```

     Появится сводка по новой группе ресурсов.
   
5. Проверьте развернутую службу перед ее выполнением. Для этого выполните команду **azure group template validate**. При тестировании развернутой службы укажите точно такие же параметры, как и при ее выполнении (как показано на следующем шаге).
   
   ```
   azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup
   ```
6. Чтобы развернуть ресурсы в группе ресурсов, выполните команду **azure group deployment create** и укажите необходимые параметры. имя развертывания, имя группы ресурсов, путь или URL-адрес шаблона, а также другие необходимые вам параметры. Если параметр **mode** не указан, используется значение по умолчанию **Incremental**. Чтобы выполнить полное развертывание, установите для параметра **mode** значение **Complete**. Будьте внимательны при использовании полного режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.
   
     Для развертывания локального шаблона используйте параметр **template-file**.
   
   ```
   azure group deployment create --resource-group examplegroup --template-file "c:\MyTemplates\example.json"
   ```
   
     Для развертывания внешнего шаблона используйте параметр **template-uri**.
   
   ```
   azure group deployment create --resource-group examplegroup --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json"
   ```
   
     В двух предыдущих примерах не указаны значения параметров. Варианты передачи значений параметров в рассматриваются в разделе [Параметры](#parameters). Пока что указать значения параметров будет предложено посредством следующего синтаксиса.

   ```
   info:    Executing command group deployment create
   info:    Supply values for the following parameters
   firstParameters:  <type here>
   ```

      После развертывания ресурсов вы увидите сводку по развертыванию. Сводка содержит значение **ProvisioningState**, указывающее, успешно ли выполнено развертывание.

   ```
   + Initializing template configurations and parameters
   + Creating a deployment
   info:    Created template deployment "example"
   + Waiting for deployment to complete
   +
   +
   data:    DeploymentName     : example
   data:    ResourceGroupName  : examplegroup
   data:    ProvisioningState  : Succeeded
   ```
7. Если вы хотите добавлять в журнал дополнительные сведения о развертывании, которые могут помочь в устранении ошибок развертывания, используйте параметр **debug-setting** . Можно задать регистрацию в журнале содержимого запроса или содержимого ответа (или и того, и другого) при операции развертывания.
   
   ```
   azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g examplegroup -n exampleDeployment
   ```

## <a name="deploy-template-from-storage-with-sas-token"></a>Развертывание шаблона из хранилища с помощью маркера SAS
Шаблоны можно добавить в учетную запись хранения и создать ссылки на них во время развертывания с помощью маркера SAS.

> [!IMPORTANT]
> Если выполнить приведенные ниже действия, то большой двоичный объект, содержащий шаблон, будет доступен только владельцу учетной записи. Тем не менее, если создать маркер SAS для этого большого двоичного объекта, то он будет доступен любому пользователю, обладающему этим универсальным кодом ресурса (URI). Если другой пользователь перехватит этот универсальный код ресурса (URI), то сможет получить доступ к шаблону. Применение маркера SAS — хороший способ ограничить доступ к своим шаблонам, но не следует указывать конфиденциальные данные, например пароли, непосредственно в шаблоне.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Добавление частного шаблона в учетную запись хранения
Ниже приведены действия по настройке учетной записи хранения для шаблонов.

1. Создайте группу ресурсов.
   
   ```
   azure group create -n "ManageGroup" -l "westus"
   ```
2. Создайте учетную запись хранения. Имя учетной записи хранения должно быть уникальным в Azure, поэтому введите собственное имя для учетной записи.
   
   ```
   azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates
   ```
3. Задайте переменные для учетной записи хранения и ключа.
   
   ```
   export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
   export AZURE_STORAGE_ACCESS_KEY={storage_account_key}
   ```
4. Создайте контейнер. Разрешение имеет значение **Off** , это означает, что контейнер доступен только владельцу.
   
   ```
   azure storage container create --container templates -p Off 
   ```
5. Добавьте свой шаблон в контейнер.
   
   ```
   azure storage blob upload --container templates -f c:\MyTemplates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>Предоставление маркера SAS во время развертывания
Чтобы развернуть частный шаблон в учетной записи хранения, извлеките маркер SAS и добавьте его в универсальный код ресурса (URI) для шаблона.

1. Создайте маркер SAS с разрешениями на чтение и сроком действия, чтобы ограничить доступ. Задайте срок действия, достаточный для выполнения развертывания. Извлеките полный универсальный код ресурса (URI) шаблона с маркером SAS.
   
   ```
   expiretime=$(date -I'minutes' --date "+30 minutes")
   fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")
   ```
2. Разверните шаблон, указав универсальный код ресурса (URI), который включает в себя маркер SAS.
   
   ```
   azure group deployment create --template-uri $fullurl -g ExampleResourceGroup
   ```

Пример использования маркера SAS со связанными шаблонами см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>Параметры

Для предоставления значений параметров доступны следующие способы: 
   
- Использование встроенных параметров. Каждый параметр имеет следующий формат: `"ParameterName": { "value": "ParameterValue" }`. В приведенном ниже примере показаны параметры с escape-символами.

   ```   
   azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment
   ```
- Использование файла параметров.

  ```    
  azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g ExampleResourceGroup -n ExampleDeployment
  ```
      

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
* Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](solution-dev-test-environments.md).
* Дополнительные сведения об использовании ссылки на хранилище ключей для безопасной передачи значений см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).
* Сведения об автоматизации развертывания см. в статье [Automating application deployments to Azure Virtual Machines](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Автоматизация развертывания приложений на виртуальных машинах Azure), которая входит в цикл из четырех частей. Этот цикл охватывает такие аспекты, как архитектура приложения, доступ и безопасность, доступность и масштабирование, а также развертывание приложения.




<!--HONumber=Jan17_HO2-->


