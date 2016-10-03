<properties
   pageTitle="Развертывание ресурсов с помощью Azure CLI и шаблона | Microsoft Azure"
   description="Узнайте, как использовать Azure Resource Manager и Azure CLI для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager."
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

# Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Интерфейс командной строки Azure](resource-group-template-deploy-cli.md)
- [Портал](resource-group-template-deploy-portal.md)
- [ИНТЕРФЕЙС REST API](resource-group-template-deploy-rest.md)

В этой статье объясняется, как использовать интерфейс командной строки Azure и шаблоны Resource Manager для развертывания ресурсов в Azure.

> [AZURE.TIP] Справку по отладке ошибок во время развертывания можно получить в следующих статьях.
>
> - Статья [Просмотр операций развертывания с помощью интерфейса командной строки Azure](resource-manager-troubleshoot-deployments-cli.md) содержит информацию о том, как получить сведения, которые помогут устранить ошибку.
> - [Устранение распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md) — руководство по устранению распространенных ошибок при развертывании.

Шаблон может быть локальным файлом или внешним файл, доступным по универсальному коду ресурса (URI). Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS).

## Быстрое развертывание

В этой статье описаны все доступные во время развертывания параметры. Но обычно достаточно двух простых команд. Чтобы быстро приступить к выполнению развертывания, воспользуйтесь следующими командами:

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

Дополнительные сведения о параметрах развертывания, которые могут быть более подходящими для вашего сценария, см. в последующих разделах этой статьи.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## Развертывание с помощью интерфейса командной строки Azure

Информацию об использовании интерфейса командной строки Azure с диспетчером ресурсов см. в статье [Использование интерфейса командной строки Azure для Mac, Linux и Windows в режиме управления ресурсами Azure](xplat-cli-azure-resource-manager.md).

1. Войдите в свою учетную запись Azure. После предоставления учетных данных команда возвращает результат вашего входа.

        azure login
  
        ...
        info:    login command OK

2. Если у вас несколько подписок, укажите подписку, которую вы хотите использовать для развертывания.

        azure account set <YourSubscriptionNameOrId>

3. Переключитесь на модуль диспетчера ресурсов Azure. Вы получите подтверждение о новом режиме.

        azure config mode arm
   
        info:     New mode is arm

4. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Введите имя группы ресурсов и расположение, необходимое для решения. Вам нужно указать расположение группы, так как она хранит метаданные ресурсов. Для соответствия требованиям вам, возможно, нужно указать, где хранятся эти метаданные. Обычно мы рекомендуем указывать расположение, в котором будет храниться большинство ресурсов. Используя одно и то же расположение, можно упростить шаблон.

        azure group create -n ExampleResourceGroup -l "West US"

     Появится сводка по новой группе ресурсов.
   
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

5. Проверьте развернутую службу перед ее выполнением. Для этого выполните команду **azure group template validate**. При тестировании развернутой службы укажите точно такие же параметры, как и при ее выполнении (как показано на следующем шаге).

        azure group template validate -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup

5. Чтобы развернуть ресурсы в группе ресурсов, выполните следующую команду и укажите необходимые параметры — имя развертывания, имя группы ресурсов, путь или URL-адрес шаблона, а также другие необходимые вам параметры.
   
     Для предоставления значений параметров доступно три следующих способа.

     1. Использование встроенных параметров и локального шаблона. Каждый параметр имеет следующий формат: `"ParameterName": { "value": "ParameterValue" }`. В приведенном ниже примере показаны параметры с escape-символами.

            azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Использование встроенных параметров и ссылки на шаблон.

            azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Использование файла параметров. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](./#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     После развертывания ресурсов посредством одного из трех методов, приведенных выше, вы увидите сводку по развертыванию.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Чтобы выполнить полное развертывание, установите для параметра **Режим** значение **Полный**. Будьте внимательны при использовании этого режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Если вы хотите добавлять в журнал дополнительные сведения о развертывании, которые могут помочь в устранении ошибок развертывания, используйте параметр **debug-setting**. Можно задать регистрацию в журнале содержимого запроса или содержимого ответа (или и того, и другого) при операции развертывания.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## Развертывание шаблона из хранилища с помощью маркера SAS

Шаблоны можно добавить в учетную запись хранения и создать ссылки на них во время развертывания с помощью маркера SAS.

> [AZURE.IMPORTANT] Если выполнить приведенные ниже действия, то большой двоичный объект, содержащий шаблон, будет доступен только владельцу учетной записи. Тем не менее, если создать маркер SAS для этого большого двоичного объекта, то он будет доступен любому пользователю, обладающему этим универсальным кодом ресурса (URI). Если другой пользователь перехватит этот универсальный код ресурса (URI), то сможет получить доступ к шаблону. Применение маркера SAS — хороший способ ограничить доступ к своим шаблонам, но не следует указывать конфиденциальные данные, например пароли, непосредственно в шаблоне.

### Добавление частного шаблона в учетную запись хранения

Ниже приведены действия по настройке учетной записи хранения для шаблонов.

1. Создайте группу ресурсов.

        azure group create -n "ManageGroup" -l "westus"

2. Создайте учетную запись хранения. Имя учетной записи хранения должно быть уникальным в Azure, поэтому введите собственное имя для учетной записи.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. Задайте переменные для учетной записи хранения и ключа.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. Создайте контейнер. Разрешение имеет значение **Off**, это означает, что контейнер доступен только владельцу.

        azure storage container create --container templates -p Off 
        
4. Добавьте свой шаблон в контейнер.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### Предоставление маркера SAS во время развертывания

Чтобы развернуть частный шаблон в учетной записи хранения, извлеките маркер SAS и добавьте его в универсальный код ресурса (URI) для шаблона.

1. Создайте маркер SAS с разрешениями на чтение и сроком действия, чтобы ограничить доступ. Задайте срок действия, достаточный для выполнения развертывания. Извлеките полный универсальный код ресурса (URI) шаблона с маркером SAS.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. Разверните шаблон, указав универсальный код ресурса (URI), который включает в себя маркер SAS.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

Пример использования маркера SAS со связанными шаблонами см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## Дальнейшие действия
- Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](virtual-machines/virtual-machines-windows-csharp-template.md).
- Сведения об определении параметров в шаблоне см. в разделе [Параметры](resource-group-authoring-templates.md#parameters).
- Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](solution-dev-test-environments.md).
- Дополнительные сведения об использовании ссылки на KeyVault для передачи безопасных значений см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0921_2016-->