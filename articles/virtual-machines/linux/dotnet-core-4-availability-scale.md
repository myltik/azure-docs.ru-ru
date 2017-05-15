---
title: "Параметры доступности и масштабирования в шаблонах Azure Resource Manager | Документация Майкрософт"
description: "Руководство по .NET Core для виртуальных машин Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8fcfea79-f017-4658-8c51-74242fcfb7f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 435ebb1fd31cc866b4a87659f4158680bd27940d
ms.contentlocale: ru-ru
ms.lasthandoff: 04/03/2017


---
# <a name="availability-and-scale-in-azure-resource-manager-templates-for-linux-vms"></a>Параметры доступности и масштабирования в шаблонах Azure Resource Manager для виртуальных машин Linux

Доступность и масштабирование обозначают время непрерывной работы и возможность соответствия требованиям к ресурсам. Если приложение должно быть доступным 99,9 % времени, его архитектура должна поддерживать несколько параллельных ресурсов вычислений. Например, вместо одного веб-сайта конфигурация с высоким уровнем доступности содержит несколько экземпляров одного сайта и использует технологию балансировки. В этой конфигурации один экземпляр приложения может быть отключен для обслуживания, а другой продолжать работать. Масштабирование, в свою очередь, обозначает возможность приложения удовлетворять требованиям. Приложение балансировки нагрузки позволяет добавлять и удалять экземпляры из пула для масштабирования приложения в соответствии с требованиями.

В этой статье описано, как настроить развертывание приложения музыкального магазина, чтобы обеспечить доступность и масштабирование. Здесь будут описаны все зависимости и уникальные настройки. Чтобы оптимизировать процесс, заранее разверните экземпляр решения в подписке Azure, а затем установите шаблон Azure Resource Manager. Полный шаблон можно найти [здесь](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="availability-set"></a>Группа доступности
Группа доступности логически охватывает виртуальные машины Azure на физических узлах и другие инфраструктурные компоненты, например источники питания и физическое сетевое оборудование. Она гарантирует, что в случае обслуживания, сбоя устройства или простоя по другой причине не все виртуальные машины будут затронуты. Чтобы добавить группу доступности в шаблон Azure Resource Manager, можно использовать мастер добавления нового ресурса в Visual Studio или вставить в шаблон допустимый объект JSON.

Чтобы увидеть пример JSON в шаблоне Resource Manager, щелкните следующую ссылку: [Группа доступности](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L387).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "avalibility-set"
  },
  "properties": {
    "platformUpdateDomainCount": 5,
    "platformFaultDomainCount": 3
  }
}
```

Группа доступности определяется как свойство ресурса виртуальной машины. 

Чтобы увидеть пример JSON в шаблоне Resource Manager, щелкните следующую ссылку: [Сопоставление группы доступности с виртуальной машиной](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L313).

```json
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
Ниже приведено представление группы доступности на портале Azure. Здесь можно увидеть каждую виртуальную машину и сведения о конфигурации.

![Группа доступности](./media/dotnet-core-4-availability-scale/aset.png)

Дополнительные сведения о группах доступности см. в статье [Управление доступностью виртуальных машин](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

## <a name="network-load-balancer"></a>Балансировщик сетевой нагрузки
В то время как группа доступности обеспечивает отказоустойчивость приложения, балансировщик нагрузки обеспечивает доступность нескольких экземпляров приложения по одному сетевому адресу. Эти экземпляры могут размещаться на нескольких виртуальных машинах, каждая из которых подключена к балансировщику нагрузки. При получении доступа к приложению балансировщик нагрузки направляет входящий запрос к подключенным экземплярам. Чтобы добавить балансировщик нагрузки в шаблон Azure Resource Manager, можно использовать мастер добавления нового ресурса в Visual Studio или вставить в шаблон правильно определенный ресурс JSON.

Чтобы увидеть пример JSON в шаблоне Resource Manager, щелкните следующую ссылку: [Балансировщик сетевой нагрузки](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-front"
  },
  ........<truncated>
}
```

Так как пример приложения доступен в Интернете по общедоступному IP-адресу, этот адрес связан с балансировщиком нагрузки. 

Чтобы увидеть пример JSON в шаблоне Resource Manager, щелкните следующую ссылку: [Сопоставление балансировщика сетевой нагрузки с общедоступным IP-адресом](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L221).

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

На портале Azure в обзоре балансировщика сетевой нагрузки можно увидеть, что он связан с общедоступным IP-адресом.

![Балансировщик сетевой нагрузки](./media/dotnet-core-4-availability-scale/nlb.png)

## <a name="load-balancer-rule"></a>Правило балансировщика нагрузки
При использовании балансировщика нагрузки настраиваются правила для распределения трафика между целевыми ресурсами. В нашем примере приложения музыкального магазина трафик поступает по общедоступному IP-адресу на порт 80 и распределяется в рамках этого порта по всем виртуальным машинам. 

Чтобы увидеть пример JSON в шаблоне Resource Manager, щелкните следующую ссылку: [Правило балансировщика нагрузки](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270).

```json
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Ниже приведено представление правила балансировщика сетевой нагрузки на портале.

![Правило балансировщика сетевой нагрузки](./media/dotnet-core-4-availability-scale/lbrule.png)

## <a name="load-balancer-probe"></a>Проба балансировщика нагрузки
Чтобы запросы обслуживались только в работающих системах, балансировщику нагрузки необходимо отслеживать каждую виртуальную машину. Для этого он постоянно проверяет предопределенный порт. Для развертывания приложения музыкального магазина настроено выполнение проверки порта 80 на всех включенных виртуальных машинах. 

Чтобы увидеть пример JSON в шаблоне Resource Manager, щелкните следующую ссылку: [Проба балансировщика нагрузки](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L257).

```json
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

Ниже приведено окно пробы балансировщика нагрузки на портале Azure.

![Проба балансировщика сетевой нагрузки](./media/dotnet-core-4-availability-scale/lbprobe.png)

## <a name="inbound-nat-rules"></a>Правила преобразования сетевых адресов для входящих подключений
При использовании балансировщика нагрузки правила следует поместить в расположение, обеспечивающее доступ без балансировки нагрузки к каждой виртуальной машине. Например, при создании SSH-подключения к каждой виртуальной машине к этому трафику не следует применять балансировку нагрузки. Вместо этого необходимо настроить предопределенный путь с использованием ресурса правила NAT для входящего трафика. С помощью этого ресурса входящий трафик можно сопоставить с отдельными виртуальными машинами. 

При использовании приложения музыкального магазина порт, начиная с 5000, сопоставляется с портом 22 на каждой виртуальной машине для SSH-доступа. Функция `copyindex()` используется для последовательного повышения номера входящего порта. Таким образом, вторая виртуальная машина получает входящий порт 5001, третья — 5002 и т. д. 

Чтобы увидеть пример JSON в шаблоне Resource Manager, щелкните следующую ссылку: [Правила NAT для входящего трафика](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270). 

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'SSH-VM', copyIndex())]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 22,
    "enableFloatingIP": false
  }
}
```

Ниже приведено представление правила NAT для входящего трафика на портале Azure. Правило NAT SSH создается для каждой виртуальной машины в развертывании.

![Правило NAT для входящего трафика](./media/dotnet-core-4-availability-scale/natrule.png)

Дополнительные сведения о балансировщике сетевой нагрузки Azure см. в статье [Балансировка нагрузки для служб инфраструктуры Azure](../virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="deploy-multiple-vms"></a>Развертывание нескольких виртуальных машин
Наконец, для эффективной работы группы доступности или балансировщика нагрузки необходимо несколько виртуальных машин. Их можно развернуть с помощью функции копирования шаблона Azure Resource Manager. В таком случае не нужно определять конечное число виртуальных машин. Это значение может быть предоставлено динамически во время развертывания. Функция копирования принимает число экземпляров для создания и обрабатывает развертывание правильного количества виртуальных машин и связанных ресурсов.

В шаблоне примера приложения музыкального магазина определен параметр, принимающий число экземпляров. Это число используется в шаблоне при создании виртуальных машин и связанных ресурсов.

```json
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 1,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
}
```

В ресурсе виртуальной машины циклу копирования присваивается имя и используется параметр числа экземпляров для управления количеством полученных копий.

Чтобы увидеть пример JSON в шаблоне Resource Manager, щелкните следующую ссылку: [Функция копирования виртуальной машины](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L300). 

```json
"apiVersion": "2015-06-15",
"type": "Microsoft.Compute/virtualMachines",
"name": "[concat(variables('vmName'),copyindex())]",
"location": "[resourceGroup().location]",
"copy": {
  "name": "virtualMachineLoop",
  "count": "[parameters('numberOfInstances')]"
}
```

Доступ к текущей итерации функции копирования можно получить с помощью функции `copyIndex()` . Значение функции копирования индекса можно использовать для присвоения имен виртуальным машинам и другим ресурсам. Например, если развернуты два экземпляра виртуальной машины, им необходимо присвоить разные имена. Функцию `copyIndex()` можно использовать как часть имени виртуальной машины для создания уникального имени. Пример функции `copyindex()` , используемой для именования, можно увидеть в ресурсе виртуальной машины. Здесь имя компьютера представляет собой объединение параметра `vmName` и функции `copyIndex()`. 

Чтобы увидеть пример JSON в шаблоне Resource Manager, щелкните следующую ссылку: [Функция копирования индекса](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L319). 

```json
"osProfile": {
  "computerName": "[concat(parameters('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "linuxConfiguration": {
    "disablePasswordAuthentication": "true",
    "ssh": {
      "publicKeys": [
        {
          "path": "[variables('sshKeyPath')]",
          "keyData": "[parameters('sshKeyData')]"
        }
      ]
    }
  }
}
```

В шаблоне примера приложения музыкального магазина функция `copyIndex` используется несколько раз. Ресурсы и функции, использующие `copyIndex`, представлены такими компонентами отдельного экземпляра виртуальной машины, как сетевой интерфейс, правила подсистемы балансировки нагрузки и пр. (в зависимости от функций). 

Дополнительные сведения о функции копирования см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](../../resource-group-create-multiple.md).

## <a name="next-step"></a>Дальнейшие действия
<hr>

[Шаг 4. Развертывание приложений с использованием шаблонов Azure Resource Manager](dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


