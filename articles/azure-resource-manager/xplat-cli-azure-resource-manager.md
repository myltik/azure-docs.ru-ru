---
title: "Управление ресурсами с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "Используйте интерфейс командной строки Azure для управления ресурсами и группами ресурсов в Azure."
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.assetid: bb0af466-4f65-4559-ac3a-43985fa096ff
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 39ccb6e9b810d1c02a96b25946893ddd3e316463


---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure
> [!div class="op_single_selector"]
> * [Портал](resource-group-portal.md) 
> * [Интерфейс командной строки Azure](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [ИНТЕРФЕЙС REST API](resource-manager-rest-api.md)
> 
> 

Интерфейс командной строки Azure (Azure CLI) — это одно из нескольких средств для развертывания ресурсов, а также управления ими с помощью Resource Manager. В этой статье описываются стандартные методы управления ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure в режиме Azure Resource Manager. Сведения о развертывании ресурсов с помощью интерфейса командной строки см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и интерфейса командной строки Azure](resource-group-template-deploy-cli.md). Общие сведения о ресурсах Azure и Resource Manager см. в статье [Общие сведения об Azure Resource Manager](resource-group-overview.md).

> [!NOTE]
> Чтобы управлять ресурсами Azure с помощью интерфейса командной строки Azure, необходимо установить [интерфейс командной строки Azure](../xplat-cli-install.md) и [войти в Azure](../xplat-cli-connect.md) с помощью команды `azure login`. Убедитесь, что интерфейс командной строки работает в режиме Resource Manager (выполните команду `azure config mode arm`). Если все это уже сделано, у вас все готово для работы.
> 
> 

## <a name="get-resource-groups-and-resources"></a>Получение сведений о ресурсах и группах ресурсов
### <a name="resource-groups"></a>Группы ресурсов
Чтобы получить список всех групп ресурсов в подписке и их расположения, выполните следующую команду.

    azure group list


### <a name="resources"></a>Ресурсы
 Чтобы получить список всех ресурсов в группе, например в группе *testRG*, выполните следующую команду.

    azure resource list testRG

Чтобы просмотреть отдельный ресурс в группе, например виртуальную машину *MyUbuntuVM*, выполните следующую команду.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Обратите внимание на параметр **Microsoft.Compute/virtualMachines**. Он указывает тип ресурса, для которого запрашивается информация.

> [!NOTE]
> При использовании команд **azure resource**, отличных от команды **list**, необходимо указать версию API ресурса с помощью параметра **-o**. Если вы не уверены, какая именно версия API используется, обратитесь к файлу шаблона и найдите поле apiVersion для этого ресурса. Дополнительные сведения о версиях API в Resource Manager см. в статье [Поставщики Resource Manager, регионы, версии API и схемы](resource-manager-supported-services.md).
> 
> 

При просмотре сведений о ресурсе часто бывает полезно использовать параметр `--json`. Он делает выходные данные более удобными для чтения, так как некоторые значения — это вложенные структуры или коллекции. В следующем примере результаты команды **show** возвращаются в виде документа JSON.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

> [!NOTE]
> При желании сохраните данные JSON в файл, используя символ &gt; для перенаправления выходных данных в файл. Например:
> 
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`
> 
> 

### <a name="tags"></a>Теги
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Управление ресурсами
Чтобы добавить ресурс, например учетную запись хранения, в группу ресурсов, выполните команду, аналогичную следующей.

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"

Наряду с параметром **-o**, с помощью которого можно указать версию API, также можно использовать параметр **-p**, чтобы передать строку с любыми обязательными или дополнительными свойствами в формате JSON.

Чтобы удалить имеющийся ресурс, например ресурс виртуальной машины, используйте следующую команду.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Чтобы переместить существующие ресурсы в другую группу или подписку, используйте команду **azure resource move** . В следующем примере показано, как переместить кэш Redis в новую группу ресурсов. В параметре **-i** укажите разделенный запятыми список идентификаторов ресурсов для перемещения.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Управление доступом к ресурсам
Интерфейс командной строки Azure можно использовать, чтобы создавать политики управления доступом к ресурсам Azure, а также управлять ими. Общие сведения об определениях политик и назначении политик ресурсам см. в статье [Применение политик для управления ресурсами и контроля доступа](resource-manager-policy.md).

Например, чтобы отклонять все запросы, полученные не из региона "Западная часть США" или "Северо-центральный регион США", определите следующую политику и сохраните ее в файл определения политики policy.json.

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

Затем выполните команду **policy definition create**.

    azure policy definition create MyPolicy -p c:\temp\policy.json

После выполнения команды должен появиться результат, аналогичный приведенному ниже.

    + Creating policy definition MyPolicy data:    PolicyName:             MyPolicy data:    PolicyDefinitionId:     /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    data:    PolicyType:             Custom data:    DisplayName:            undefined data:    Description:            undefined data:    PolicyRule:             field=location, in=[westus, northcentralus], effect=deny

 Чтобы назначить политику в определенной области, используйте свойство **PolicyDefinitionId**, возвращенное в предыдущей команде. В следующем примере в качестве этой области выступает подписка, но политику также можно назначить группе ресурсов или отдельным ресурсам.

    azure policy assignment create MyPolicyAssignment -p /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/########-####-####-####-############/

С помощью команд **policy definition show**, **policy definition set** и **policy definition delete** можно получить, изменить или удалить определения политики.

Аналогичным образом с помощью команд **policy assignment show**, **policy assignment set** и **policy assignment delete** можно получить, изменить или удалить назначения политики.

## <a name="export-a-resource-group-as-a-template"></a>Экспорт группы ресурсов в виде шаблона
Вы можете просмотреть шаблон Resource Manager для любой существующей группы ресурсов. Экспорт шаблона обеспечивает два преимущества:

1. Последующие развертывания решения можно с легкостью автоматизировать, так как в шаблоне определены все компоненты инфраструктуры.
2. Вы можете ознакомиться с синтаксисом шаблона, просмотрев представление JSON для своего решения.

С помощью интерфейса командной строки Azure вы можете экспортировать шаблон, который представляет текущее состояние группы ресурсов, или скачать шаблон, который использовался для конкретного развертывания.

* **Экспорт шаблона для группы ресурсов** удобно использовать, если вы изменили группу ресурсов и хотите получить представление JSON ее текущего состояния. Однако созданный шаблон содержит только минимальное число параметров, и в нем не указаны какие-либо переменные. Большая часть значений в шаблоне заданы жестко. Прежде чем развертывать созданный шаблон, можно преобразовать дополнительные значения в параметры, чтобы настроить развертывание для разных сред.
  
    Чтобы экспортировать шаблон для группы ресурсов в локальный каталог, выполните команду `azure group export`, как показано в следующем примере. (Замените указанный в примере локальный каталог каталогом, используемым в вашей операционной системе.)
  
        azure group export testRG ~/azure/templates/
* **Экспорт шаблона для конкретного развертывания** удобно использовать, если нужно просмотреть шаблон, с помощью которого были развернуты ресурсы. Этот шаблон содержит все параметры и переменные, определенные для исходного развертывания. Тем не менее если кто-то в организации внес изменения в группу ресурсов, в частности в параметры вне шаблона, этот шаблон не будет представлять текущее состояние группы ресурсов.
  
    Чтобы скачать в локальный каталог шаблон для конкретного развертывания, выполните команду `azure group deployment template download`. Например:
  
        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/

> [!NOTE]
> Так как функция экспорта шаблона пока доступна в предварительной версии, поддерживаются не все типы ресурсов. При попытке экспорта шаблона может появиться сообщение о том, что некоторые ресурсы не экспортированы. При необходимости эти ресурсы можно определить вручную после скачивания шаблона.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об операциях развертывания и устранении неполадок, связанных с развертыванием, см. в статье [Просмотр операций развертывания с помощью интерфейса командной строки Azure](resource-manager-troubleshoot-deployments-cli.md).
* Сведения о настройке приложения или скрипта для доступа к ресурсам с помощью интерфейса командной строки см. в статье [Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам](resource-group-authenticate-service-principal-cli.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).




<!--HONumber=Dec16_HO2-->


