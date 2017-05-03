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
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c889a609b8d49474216fe1dcfba69a881edb4133
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI

В этом разделе объясняется, как использовать [Azure CLI 2.0](/cli/azure/install-az-cli2) и шаблоны Resource Manager для развертывания ресурсов в Azure.  Шаблон может быть локальным файлом или внешним файл, доступным по универсальному коду ресурса (URI).

Вы можете получить шаблон (storage.json), используемый в этих примерах, в статье [Создание первого шаблона Azure Resource Manager](resource-manager-create-first-template.md#final-template). Чтобы использовать шаблон с этими примерами, создайте JSON-файл и добавьте в него скопированное содержимое.

## <a name="deploy-local-template"></a>Развертывание локального шаблона

Чтобы быстро приступить к развертыванию, используйте следующие команды для развертывания локального шаблона со встроенными параметрами.

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

Развертывание может занять несколько минут. По завершении появится сообщение, содержащее результат:

```azurecli
"provisioningState": "Succeeded",
```

Предыдущий пример создавал группу ресурсов в подписке по умолчанию. Чтобы использовать другую подписку, добавьте команду [az account set](/cli/azure/account#set) после входа.

## <a name="deploy-external-template"></a>Развертывание внешнего шаблона

Для развертывания внешнего шаблона используйте параметр **template-uri**. Шаблон может иметь любой общедоступный код URI (например, быть файлом в учетной записи хранения).
   
```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

Шаблон можно защитить, запрашивая токен подписанного URL-адреса (SAS) для доступа. Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-cli-sas-token.md).

## <a name="parameter-files"></a>Файлы параметров

В предыдущих примерах показано, как передать параметры в виде встроенных значений. Вы можете указать значения параметров в файле и передать его при развертывании. 

Файл параметров должен быть в указанном ниже формате.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

Чтобы передать локальный файл параметров, используйте:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-deployment"></a>Тестирование развертывания

Чтобы проверить шаблон и значения параметров без фактического развертывания ресурсов, используйте [az group deployment validate](/cli/azure/group/deployment#validate). Он имеет те же параметры для использования локальных или удаленных файлов.

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="debug"></a>Отладка

Чтобы просмотреть сведения об операциях для развертывания, завершившегося сбоем, используйте следующую команду.
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).


## <a name="export-resource-manager-template"></a>Экспорт шаблона Resource Manager
Для существующей группы ресурсов (развернутой с помощью Azure CLI или других средств, например через портал) можно просмотреть шаблон Resource Manager. Экспорт шаблона обеспечивает два преимущества:

1. Последующие развертывания решения можно с легкостью автоматизировать, так как в шаблоне определены все компоненты инфраструктуры.
2. Вы можете ознакомиться с синтаксисом шаблона, просмотрев представление JSON решения.

Чтобы просмотреть шаблон для группы ресурсов, выполните команду [az group export](/cli/azure/group#export).

```azurecli
az group export --name ExampleGroup
```

Дополнительные сведения см. в статье [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Чтобы использовать полный режим, используйте параметр `mode`.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```


## <a name="next-steps"></a>Дальнейшие действия
* Полный пример сценария, развертывающего шаблон, см. в статье [Сценарий для развертывания шаблона Resource Manager](resource-manager-samples-cli-deploy.md).
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-cli-sas-token.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).

