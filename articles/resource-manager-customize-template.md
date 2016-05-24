<properties
	pageTitle="Настройка экспортированного шаблона Resource Manager | Microsoft Azure"
	description="Добавление параметров в экспортированный шаблон Azure Resource Manager и его повторное развертывание с помощью Azure PowerShell или интерфейса командной строки Azure."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/10/2016"
	ms.author="tomfitz"/>

# Настройка экспортированного шаблона Azure Resource Manager

В этой статье показано, как изменить экспортированный шаблон для передачи дополнительных значений в качестве параметров. Это руководство основывается на инструкциях из статьи [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md), но вам необязательно предварительно выполнять их. В этой статье приведены необходимые шаблоны и скрипты.

## Просмотр экспортированного шаблона

Если вы выполнили шаги руководства по [экспорту шаблона Resource Manager](resource-manager-export-template.md), откройте скачанный шаблон. Он называется **template.json**. Изменить шаблон можно с помощью Visual Studio или Visual Code. Можно также использовать любой редактор JSON или текстовый редактор.

Если вы не работали с предыдущим руководством, создайте файл с именем **template.json** и добавьте в этот файл следующее содержимое из экспортированного шаблона.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
        },
        "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

Шаблон template.json можно использовать для создания однотипных учетных записей хранения в одном и том же регионе в рамках виртуальной сети, для которой при каждом развертывании используется один и тот же префикс адреса и префикс подсети. Тем не менее Resource Manager обеспечивает более гибкое развертывание, чем этот шаблон. Например, во время развертывания вы можете указывать тип создаваемой учетной записи хранения или значения, используемые в качестве префикса адреса и префикса подсети виртуальной сети.

## Настройка шаблона

В этом разделе вы добавите параметры в экспортированный шаблон, чтобы его можно было использовать снова при развертывании тех же ресурсов в других средах. Кроме того, мы добавим в шаблон некоторые функции, чтобы снизить риск возникновения ошибки при его развертывании. Для учетной записи хранения больше не нужно подбирать уникальное имя. Шаблон сделает это за вас. Вы ограничите значения, которые можно указывать для типа учетной записи хранения, только до допустимых вариантов.

1. Чтобы можно было передавать значения, которые можно указывать при развертывании, введите в разделе **parameters** указанные ниже определения. Обратите внимание на значения **allowedValues** для переменной **storageAccount\_accountType**. Если случайно указать недопустимое значение, ошибка будет распознана до начала развертывания. Кроме того, обратите внимание, что для имени учетной записи указывается только префикс, длина которого ограничена 11 знаками. Такое ограничение префикса гарантирует, что полное имя не превысит максимальное количество знаков для имени учетной записи хранения. Префикс позволяет применять соглашение об именовании к учетным записям хранения. Сведения о создании уникального имени описаны на следующем шаге.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. Раздел **variables** шаблона сейчас пуст. Введите в нем код, указанный ниже. В разделе **variables** вы как автор шаблона можете создавать значения, чтобы упростить синтаксис остальной части. Переменная **storageAccount\_name** объединяет префикс параметра с уникальной строкой, созданной на основе идентификатора группы ресурсов.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Чтобы использовать раздел параметров и переменную в определениях ресурсов, укажите в разделе **resources** приведенные ниже определения. Обратите внимание, что кроме значений, назначенных свойствам, в определениях ресурсов мало что изменилось. Эти свойства точно соответствуют свойствам экспортированного шаблона. Вы просто назначаете свойства значениям параметров, вместо того чтобы жестко задавать значения. С помощью выражения **resourceGroup().location** для расположения ресурсов определено использование того же расположения, что и для группы ресурсов. Ссылка на переменную, созданную для имени учетной записи хранения, указывается с помощью выражения **variables**.

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

## Исправление файла параметров

Параметры скачанного файла больше не соответствуют параметрам в шаблоне. Использовать файл параметров необязательно, но это упрощает повторное развертывание среды. Вы будете использовать значения по умолчанию, определенные в шаблоне для многих параметров, поэтому из файла параметров нужны только два значения.

Замените содержимое файла parameters.json следующим:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

Обновленный файл параметров определяет значения только для параметров без значений по умолчанию. Также можно указать значения для других параметров, если вы не хотите использовать значения по умолчанию.

## Развертывание шаблона

Чтобы развернуть настроенный шаблон и файл параметров, можно использовать Azure PowerShell или интерфейс командной строки Azure. При необходимости установите [Azure PowerShell](powershell-install-configure.md) или [интерфейс командной строки Azure](xplat-cli-install.md). Вы можете использовать скрипты, скачанные вместе с шаблоном во время экспорта исходного шаблона, или написать свой скрипт для развертывания шаблона. В этой статье описаны оба метода.

2. Чтобы использовать для развертывания свой скрипт, сделайте следующее.

     При использовании PowerShell выполните следующее:

        # login
        Add-AzureRmAccount

        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json

     При использовании интерфейса командной строки Azure выполните следующее:

        azure login

        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. Если вы скачали экспортированный шаблон и скрипты, найдите файл **deploy.ps1** (для PowerShell) или файл **deploy.sh** (для интерфейса командной строки Azure).

     При использовании PowerShell выполните следующее:

        Get-Item deploy.ps1 | Unblock-File

        .\deploy.ps1

     При использовании интерфейса командной строки Azure выполните следующее:

        .\deploy.sh

## Дальнейшие действия

- [Пошаговое руководство по созданию шаблона Resource Manager](resource-manager-template-walkthrough.md) основано на действиях, выполненных в этом разделе; из него вы узнаете, как создать шаблон для более сложного решения. Вы также узнаете больше о доступных ресурсах и о том, как определить, какие значения следует указывать.
- Чтобы узнать, как экспортировать шаблон с помощью PowerShell, см. статью [Использование Azure PowerShell с Azure Resource Manager](powershell-azure-resource-manager.md).
- Чтобы узнать, как экспортировать шаблон с помощью интерфейса командной строки Azure, см. статью [Использование Azure CLI для Mac, Linux и Windows с Azure Resource Manager](xplat-cli-azure-resource-manager.md).
- Сведения о структуре шаблонов см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0518_2016-->