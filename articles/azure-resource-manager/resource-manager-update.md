---
title: "Обновление ресурса в шаблонах Azure Resource Manager | Документация Майкрософт"
description: "Описывается, как расширить функциональные возможности шаблонов Azure Resource Manager для обновления ресурса."
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/02/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7807b3f6135ede80cd7e7e6486364482cde72e29
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---updating-a-resource"></a>Примеры расширения функциональных возможностей шаблонов Azure Resource Manager. Обновление ресурса

Существует несколько сценариев, в которых во время развертывания необходимо обновить ресурс. Такой сценарий возникает, когда не удается указать все свойства ресурса, пока не созданы другие (зависимые) ресурсы. Например, при создании внутреннего пула для подсистемы балансировки нагрузки на виртуальных машинах можно обновить сетевые интерфейсы (NIC), чтобы включить их во внутренний пул. Resource Manager поддерживает обновление ресурсов во время развертывания, но необходимо правильно разработать шаблон, чтобы избежать ошибок и гарантировать, что развертывание будет обработано как обновление.

## <a name="understand-the-pattern"></a>Общие сведения о подходе

Сначала необходимо один раз сослаться на ресурс в шаблоне, чтобы его создать, но затем необходимо снова сослаться на этот ресурс, используя то же самое имя, чтобы его обновить. Необходимо учитывать, что если в шаблоне два ресурса имеют одинаковые имена, то Resource Manager порождает исключение. Во избежание этой ошибки укажите обновленный ресурс во втором шаблоне, который связан с первым или включен как вложенный шаблон с помощью типа ресурса `Microsoft.Resources/deployments`.

Затем необходимо указать во втором шаблоне имя существующего свойства, которое будет изменено, или новое имя свойства, которое будет добавлено. Затем необходимо также указать исходные свойства и их исходные значения. Если не указать исходные свойства и значения, то Resource Manager предполагает, что вы хотите создать новый ресурс, поэтому удаляет исходный ресурс. Он заменяет исходный ресурс новым ресурсом, который содержит только новые свойства, указанные вами.

Наконец, необходимо сделать ресурс зависимым от всех связанных ресурсов, которые требуется развернуть. Эта зависимость обеспечит создание ресурсов в правильном порядке. Порядок должен быть следующим:

1. Создается ресурс.
2. Создаются зависимые ресурсы.
3. Ресурс (из шага 1) обновляется с помощью значений из зависимых ресурсов (шаг 2).

## <a name="example-template"></a>Пример шаблона

Ниже приведен пример шаблона, демонстрирующий этот подход. Развертывается виртуальная сеть с именем `firstVNet`, которая включает в себя одну подсеть с именем `firstSubnet`. Затем развертывается виртуальный сетевой интерфейс (NIC) с именем `nic1`, который связывается с подсетью. Затем ресурс развертывания `updateVNet` включает в себя вложенный шаблон, который ссылается на имя ресурса `firstVNet`. 

Просмотрите свойство `addressSpace` и свойство `subnets` этого ресурса. Обратите внимание, что для свойства `addressSpace` задано такое же значение, как в объекте развертывания ресурсов `firstVNet`. Аналогичным образом в массиве `subnets` задано значение для `firstSubnet`. Так как все исходные свойства `firstVNet` указаны, Resource Manager обновляет ресурс в Azure. В данном случае обновление заключается в добавлении новой подсети с именем `secondSubnet`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[              
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

## <a name="try-the-template"></a>Пробное использование шаблона

Если вы хотите поэкспериментировать с этим шаблоном, выполните следующее.

1.    Перейдите на портал Azure, щелкните значок "+" и найдите тип ресурса "Развертывание шаблона". Выберите его в результатах поиска.
2.    На открывшейся странице "Развертывание шаблона" нажмите кнопку **Создать**. В результате откроется колонка "Настраиваемое развертывание".
3.    Щелкните значок **Изменить**.
4.    Удалите пустой шаблон.
5.    Скопируйте и вставьте предыдущий пример шаблона в область справа.
6.    Нажмите кнопку **Сохранить**.
7.    Вы вернетесь в область "Настраиваемое развертывание", но на этот раз в ней будет несколько раскрывающихся списков. Выберите свою подписку, создайте новую или используйте существующую группу ресурсов и выберите расположение. Ознакомьтесь с условиями и нажмите кнопку **Принимаю**.
8.    Нажмите кнопку **Приобрести**.

По завершении развертывания откройте группу ресурсов, которую вы указали на портале. Вы увидите виртуальную сеть `firstVNet` и виртуальный сетевой интерфейс `nic1`. Щелкните `firstVNet`, а затем — `subnets`. Отобразится подсеть `firstSubnet`, созданная изначально, а также подсеть `secondSubnet`, добавленная в ресурсе `updateVNet`. 

![Исходная подсеть и обновленная подсеть](./media/resource-manager-update/firstVNet-subnets.png)

Затем вернитесь в группу ресурсов и щелкните `nic1`, а затем — `IP configurations`. В разделе `IP configurations` для `Subnet` задано значение `firstSubnet (10.0.0.0/24)`. 

![Конфигурации IP виртуального сетевого интерфейса nic1](./media/resource-manager-update/nic1-ipconfigurations.png)

Исходная виртуальная сеть `firstVNet` была обновлена, а не создана повторно. Если бы `firstVNet` была создана повторно, то виртуальный сетевой интерфейс `nic1` не был бы связан с `firstVNet`.

## <a name="next-steps"></a>Дальнейшие действия

Этот подход можно использовать в шаблонах, чтобы изменить исходные значения свойств ресурса, который требуется обновить. Укажите обновленный ресурс в связанном или вложенном шаблоне с помощью типа ресурса `Microsoft.Resources/deployments`.

* Чтобы ознакомиться с функцией `reference()`, см. статью [Функции шаблона Resource Manager](resource-group-template-functions.md).
* Этот подход также реализован в [проекте стандартных блоков шаблона](https://github.com/mspnp/template-building-blocks) и [эталонных архитектурах Azure](/azure/architecture/reference-architectures/).
