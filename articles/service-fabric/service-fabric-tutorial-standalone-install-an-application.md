---
title: Руководство по установке приложения в изолированном кластере Service Fabric — Azure Service Fabric | Документация Microsoft
description: Из этого руководства вы узнаете, как установить приложение в изолированном кластере Service Fabric.
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
ms.openlocfilehash: 65c0ca98393fbb4f54eee2f4864218f231765904
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208579"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Руководство по развертыванию приложения в изолированном кластере Service Fabric

Изолированные кластеры Service Fabric предоставляют возможность выбрать собственную среду и создать кластер в рамках подхода "любая ОС, любое облако", который применяется на платформе Service Fabric. Из этой серии руководств вы узнаете, как создать изолированный кластер, размещенный в AWS, и развернуть в нем приложение.

Это руководство представляет собой первую часть цикла.  Изолированные кластеры Service Fabric предоставляют возможность выбрать собственную среду и создать кластер в рамках подхода "любая ОС, любое облако", который применяется в Service Fabric. В этом руководстве показано, как создать инфраструктуру AWS, необходимую для размещения этого изолированного кластера.

В третьей части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * загрузка примера приложения;
> * развертывание в кластере.

## <a name="prerequisites"></a>предварительным требованиям

Перед началом работы с этим руководством выполните следующие действия:

* [Установите Visual Studio 2017](https://www.visualstudio.com/), а также рабочие нагрузки **разработка Azure** и **ASP.NET и веб-разработка**.
* [Установка пакета SDK для Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Скачивание примера приложения для голосования

В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Развертывание приложения в кластер Service Fabric

После скачивания приложения можно развернуть его в кластере напрямую из Visual Studio.

1. Запустите Visual Studio

2. Выберите **Файл** > **Открыть**.

3. Перейдите в папку, в которую вы клонировали репозиторий Git, и выберите файл Voting.sln.

4. Щелкните правой кнопкой мыши проект приложения `Voting` в обозревателе решений, а затем выберите **Опубликовать**.

5. Щелкните раскрывающийся список **Конечная точка подключения** и введите общедоступное DNS-имя одного из узлов в кластере.  Например, `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`

6. Откройте любой браузер и введите адрес кластера — конечную точку подключения и порт, на котором развертывается приложение (в нашем случае это порт 8080), например: ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080.

    ![Ответ API из кластера](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Дополнительная информация

Из третьей части этой серии вы узнали, как развернуть приложение в кластере, выполнив следующие задачи:

> [!div class="checklist"]
> * загрузка примера приложения;
> * развертывание в кластере.

Перейдите к четвертой части серии руководств, в которой объясняется, как очистить кластер.

> [!div class="nextstepaction"]
> [Очистка ресурсов](service-fabric-tutorial-standalone-clean-up.md)