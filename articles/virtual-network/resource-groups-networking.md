---
title: "Общие сведения о поставщике сетевых ресурсов | Документация Майкрософт"
description: "Узнайте о новом поставщике сетевых ресурсов в диспетчере ресурсов Azure"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 79bf09da-4809-45cb-8d21-705616ef24dc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 2428c707ddeed281fddd1e57bc5574603f0b9b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="network-resource-provider"></a>Поставщик сетевых ресурсов
В современных условиях для успеха бизнеса необходима возможность быстрого, гибкого, безопасного и воспроизводимого создания и администрирования крупномасштабных сетевых приложений. Azure Resource Manager позволяет создавать такие приложения в виде единой коллекции ресурсов в группах ресурсов. Для управления такими ресурсами используются различные поставщики ресурсов в Azure Resource Manager.

Диспетчер ресурсов Azure использует разных поставщиков ресурсов для предоставления доступа к вашим ресурсам. Существует три основных поставщика ресурсов: сеть, хранилище и вычисления. В этом документе описываются характеристики и преимущества поставщика сетевых ресурсов, в том числе:

* **Метаданные** : вы можете добавлять сведения к ресурсам с помощью тегов. Эти теги можно применять для отслеживания использования ресурсов в группах ресурсов и подписках.
* **Повышенный контроль над сетью** : сетевые ресурсы нежестко связаны, и ими можно управлять более детально. Это обеспечивает большую гибкость при управлении сетевыми ресурсами.
* **Ускоренная настройка** : так как сетевые ресурсы нежестко связаны, вы можете создавать и организовывать сетевые ресурсы в параллельном режиме. Это значительно сокращает время настройки.
* **Управление доступом на основе ролей (RBAC)** : RBAC предоставляет роли по умолчанию с определенной областью безопасности, а также позволяет создавать пользовательские роли для безопасного управления.
* **Упрощенное управление и развертывание** : стало проще развертывать приложения и управлять ими, так как вы можете создать весь стек приложений в виде одной коллекции ресурсов в группе ресурсов. Кроме того, развертывание выполняется быстрее, так как для этого достаточно предоставить полезные данные JSON шаблона.
* **Быстрая пользовательская настройка** : вы можете использовать шаблоны в декларативном стиле для быстрой и воспроизводимой настройки развертываний.
* **Воспроизводимая пользовательская настройка** : вы можете использовать шаблоны в декларативном стиле для быстрой и воспроизводимой настройки развертываний.
* **Интерфейсы управления** : вы можете использовать любой из следующих интерфейсов для управления ресурсами:
  * API на основе REST
  * PowerShell
  * ПАКЕТ SDK .NET
  * Пакет SDK для Node.js
  * Пакет SDK для Java
  * Инфраструктура CLI Azure
  * Портал предварительной версии
  * Язык шаблонов Resource Manager

## <a name="network-resources"></a>Сетевые ресурсы
Теперь вы можете управлять сетевыми ресурсам независимо друг от друга, а не через один вычислительный ресурс (виртуальную машину). Это обеспечивает более высокую степень гибкости и динамичности при создании сложной крупномасштабной инфраструктуры в группе ресурсов.

Ниже показано концептуальное представление примера развертывания, включающего многоуровневое приложение. Всеми ресурсами, например сетевыми адаптерами, общедоступными IP-адресами и виртуальными машинами, можно управлять независимо друг от друга.

![Модель сетевых ресурсов](./media/resource-groups-networking/Figure2.png)

Каждый ресурс содержит как общий, так и собственный набор свойств. Общие свойства:

| Свойство | ОПИСАНИЕ | Примеры значений |
| --- | --- | --- |
| **name** |Уникальное имя ресурса. Для каждого типа ресурсов действуют свои ограничения на имена. |PIP01, VM01, NIC01 |
| **расположение** |Регион Azure, в котором находится ресурс |westus, eastus |
| **id** |Уникальный идентификатор на основе универсального кода ресурса (URI) |/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP |

Вы можете ознакомиться с отдельными свойствами ресурсов в приведенных ниже подразделах.

[!INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[!INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[!INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[!INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[!INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[!INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[!INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[!INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[!INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[!INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Интерфейсы управления
Вы можете управлять сетевыми ресурсами Azure с помощью различных интерфейсов. В этом документе основное внимание уделяется двум из этих интерфейсов: интерфейсу REST API и шаблонам.

### <a name="rest-api"></a>ИНТЕРФЕЙС REST API
Как упоминалось ранее, сетевыми ресурсами можно управлять через разнообразные интерфейсы, включая API REST, пакет SDK для .NET, пакет SDK для Node.JS, пакет SDK для Java, PowerShell, CLI, портал Azure и шаблоны.

API Rest соответствует спецификации протокола HTTP 1.1. Общая структура URI API показана ниже:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Параметры в фигурных скобках представляют следующие элементы:

* **subscription-id** : идентификатор вашей подписки на Azure.
* **resource-provider-namespace** : пространство имен используемого поставщика. Значение для поставщика сетевых ресурсов — *Microsoft.Network*.
* **region-name** : имя региона Azure.

При выполнении вызовов к API REST поддерживаются следующие методы HTTP:

* **PUT** : служит для создания ресурса заданного типа, изменения свойства ресурса или изменения сопоставления ресурсов.
* **GET** : используется для получения сведений о подготовленном ресурсе.
* **DELETE** : позволяет удалить существующий ресурс.

Запрос и ответ соответствуют формату полезных данных JSON. Дополнительные сведения см. в статье [API управления ресурсами Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="resource-manager-template-language"></a>Язык шаблонов Resource Manager
Кроме принудительного управления ресурсами (через API или SDK), вы также можете использовать декларативный стиль программирования для создания сетевых ресурсов и управления ими на основе языка шаблонов Resource Manager.

Ниже приведен пример такого шаблона.

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

Шаблон — это в первую очередь описание (в формате JSON) ресурсов и значений экземпляров, введенных с помощью параметров. Пример ниже можно использовать для создания виртуальной сети с двумя подсетями.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Вы можете указывать значения параметров вручную при использовании шаблона или же применять для этого файл параметров. В примере ниже показан возможный набор значений параметров, который используется для указанного выше шаблона:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


Основные преимущества использования шаблонов заключаются в следующем:

* Вы можете создать сложную инфраструктуру в группе ресурсов, используя декларативный стиль. За организацию создания ресурсов, включая управление зависимостями, отвечает Resource Manager.
* Созданную инфраструктуру можно воспроизводить в различных регионах и в пределах одного региона путем простого изменения параметров.
* Декларативный стиль позволяет сократить время подготовки при создании шаблонов и развертывании инфраструктуры.

Примеры шаблонов см. в разделе [шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates).

Дополнительные сведения о языке шаблонов Resource Manager см. в статье [Язык шаблонов в Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Пример шаблона, приведенный выше, использует виртуальную сеть и ресурсы подсети. Существует и другие сетевые ресурсы, которые вы можете использовать. Они перечислены ниже.

### <a name="using-a-template"></a>Использование шаблона
Вы можете развернуть службы в Azure из шаблона с помощью PowerShell или интерфейса командной строки Azure. Кроме этого, их также можно развернуть с GitHub, щелкнув необходимую команду. Чтобы развернуть службы из шаблона на сайте GitHub, сделайте следующее:

1. Откройте файл template3 на GitHub. В качестве примера откройте страницу с шаблоном [Виртуальная сеть с двумя подсетями](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Щелкните **Развертывание в Azure**, а затем войдите на портал Azure под своими учетными данными.
3. Проверьте шаблон и нажмите кнопку **Сохранить**.
4. Щелкните **Изменить параметры** и выберите расположение для виртуальной сети и подсетей, например *Запад США*.
5. При необходимости измените значения параметров **ADDRESSPREFIX** и **SUBNETPREFIX** и нажмите кнопку **ОК**.
6. Щелкните **Выбрать группу ресурсов** , а затем выберите группу ресурсов, в которую необходимо добавить виртуальную сеть и подсети. Кроме того, можно создать новую группу ресурсов, щелкнув **Создать новую**.
7. Нажмите кнопку **Создать**. Обратите внимание на заголовок **Подготовка развертывания шаблона**. После развертывания появится экран, показанный ниже.

![Пример развертывания шаблона](./media/resource-groups-networking/Figure6.png)

## <a name="next-steps"></a>Дополнительная информация
[Язык шаблонов в диспетчере ресурсов Azure](../azure-resource-manager/resource-group-authoring-templates.md)

[Сеть Azure: часто используемые шаблоны](https://github.com/Azure/azure-quickstart-templates)

[Azure Resource Manager и классическое развертывание](../azure-resource-manager/resource-manager-deployment-model.md)

[Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)

