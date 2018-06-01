---
title: Руководство по очистке изолированного кластера Service Fabric — Azure Service Fabric | Документация Майкрософт
description: Из этого руководства вы узнаете, как выполнить очистку изолированного кластера
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: d92712200920f135e362e15e1d9536dc2342ebaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211095"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Руководство по очистке изолированного кластера

Изолированные кластеры Service Fabric предоставляют возможность выбрать собственную среду и создать кластер в рамках подхода "любая ОС, любое облако", который применяется на платформе Service Fabric. Из этой серии руководств вы узнаете, как создать изолированный кластер, размещенный в AWS, и установить в нем приложение.

Это руководство представляет собой четвертую часть цикла. В этой части руководства показано, как очистить ресурсы AWS, которые были созданы для размещения кластера Service Fabric.

В четвертой части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * очистка кластера Service Fabric;
> * очистка ресурсов AWS.

## <a name="clean-up-service-fabric-cluster"></a>Очистка кластера Service Fabric

* Подключитесь по протоколу RDP к экземпляру EC2, который использовался для установки Service Fabric.
* Откройте PowerShell.
* Перейдите в каталог извлеченной папки из второго руководства.
* Чтобы удалить кластер Service Fabric, выполните следующую команду:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* При появлении запроса выберите `Y`. Если операция выполнена успешно, выходные данные будут выглядеть так, как показано ниже, но вместо приведенных здесь примеров будут указаны ваши IP-адреса.

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>Очистка ресурсов AWS

* Войдите в учетную запись AWS.
* Перейдите к консоли EC2.
* Выберите три узла, созданные в первой части этой серии руководств.
* Щелкните **Действия** > **Состояние экземпляра** > **Завершить**.

## <a name="next-steps"></a>Дополнительная информация

Из четвертой части этой серии вы узнали, как очистить ресурсы, созданные на предыдущих этапах.

> [!div class="checklist"]
> * Очистка ресурсов

> [!div class="nextstepaction"]
> [В начало](service-fabric-tutorial-standalone-create-infrastructure.md)