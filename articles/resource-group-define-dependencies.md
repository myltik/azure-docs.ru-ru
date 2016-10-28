<properties
   pageTitle="Зависимости в шаблонах диспетчера ресурсов | Microsoft Azure"
   description="В этой статье описан способ определения зависимостей между ресурсами во время развертывания для обеспечения правильного порядка развертывания ресурсов."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# Определение зависимостей в шаблонах диспетчера ресурсов Azure

У заданного ресурса могут быть другие ресурсы, которые должны существовать до его развертывания. Например, сервер SQL Server должен существовать до развертывания базы данных SQL. Эта связь определяется путем пометки одного ресурса как зависимого от другого. Как правило, зависимость определяется с помощью элемента **dependsOn**, однако это можно также сделать с помощью функции **reference**.

Диспетчер ресурсов оценивает зависимости между ресурсами и развертывает эти ресурсы согласно установленным зависимостям. Если ресурсы не зависят друг от друга, диспетчер ресурсов развертывает их параллельно.

## Свойство dependsOn

В шаблоне элемент dependsOn позволяет определить один ресурс как зависимый от одного или нескольких ресурсов. В качестве значения свойства может выступать список имен ресурсов с разделителями-запятыми.

В следующем примере показан масштабируемый набор виртуальных машин, который зависит от балансировщика нагрузки, виртуальная сеть и цикл, который создает несколько учетных записей хранения. Остальные ресурсы не показаны в этом примере, но они должны существовать в другом месте в шаблоне.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Чтобы определить зависимость между ресурсом и ресурсами, которые создаются с помощью цикла копирования, укажите в значении элемента dependsOn имя цикла. Пример см. в статье [Создание нескольких экземпляров ресурсов в диспетчере ресурсов Azure](resource-group-create-multiple.md).

Если вы склонны использовать свойство dependsOn для сопоставления связей между ресурсами, то вам важно понимать, зачем вы это делаете. Дело в том, что такое решение может негативно влиять на производительность развертывания. Например, для документирования связей между ресурсами использование свойства dependsOn будет неправильным решением. После развертывания невозможно запросить, какие ресурсы были определены в элементе dependsOn. Использование свойства dependsOn потенциально влияет на время развертывания, так как Resource Manager не может выполнять развертывание двух зависимых ресурсов параллельно. Для документирования связей между ресурсами воспользуйтесь [привязкой ресурсов](resource-group-link-resources.md).

## Дочерние ресурсы

Свойство resources позволяет указать дочерние ресурсы, связанные с определяемым ресурсом. Дочерние ресурсы можно определять максимум на пяти нижестоящих уровнях. Важно отметить, что неявная зависимость между дочерним и родительским ресурсами не создается. Если вам нужно, чтобы дочерний ресурс был развернут после родительского ресурса, эту зависимость необходимо явно указать в свойстве dependsOn.

Каждый родительский ресурс принимает в качестве дочерних ресурсов только определенные типы ресурсов. Принимаемые типы ресурсов указаны в [схеме шаблона](https://github.com/Azure/azure-resource-manager-schemas) родительского ресурса. Имя типа дочернего ресурса содержит имя типа родительского ресурса, например: **Microsoft.Web/sites/config** и **Microsoft.Web/sites/extensions** являются дочерними ресурсами по отношению к **Microsoft.Web/sites**.

В следующем примере показаны SQL Server и база данных SQL. Обратите внимание, что несмотря на то, что база данных является дочерним ресурсом по отношению к серверу, между базой данных SQL и сервером SQL Server определена явная зависимость.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## Функция reference

[Функция reference](resource-group-template-functions.md#reference) позволяет выражению получать его значение из других пар "имя JSON — значение" или ресурсов среды выполнения. Выражения со ссылками неявно объявляют, что один ресурс зависит от другого.

    reference('resourceName').propertyPath

Зависимости можно указать как с помощью этой функции, так и с помощью свойства dependsOn, но использовать оба варианта для одного зависимого ресурса не нужно. По возможности используйте неявные ссылки, чтобы избежать случайного добавления ненужных зависимостей.

Дополнительные сведения см. в разделе о [функции reference](resource-group-template-functions.md#reference).

## Дальнейшие действия

- Сведения о создании шаблонов диспетчера ресурсов Azure см. в статье [Создание шаблонов](resource-group-authoring-templates.md).
- Список доступных в шаблоне функций см. в статье [Функции шаблонов](resource-group-template-functions.md).

<!---HONumber=AcomDC_0914_2016-->