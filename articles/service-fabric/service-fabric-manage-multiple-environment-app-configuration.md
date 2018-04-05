---
title: Управление приложениями в нескольких средах в Azure Service Fabric | Документация Майкрософт
description: Приложения Azure Service Fabric могут выполняться в кластерах, содержащих от одного до нескольких тысяч компьютеров. Иногда для этих различных сред необходимо по-разному настроить приложение. В этой статье объясняется, как определить различные параметры приложения в каждой среде.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: a3d0770d3b9c8702dbe4dac86f86030bea4090c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="manage-applications-for-multiple-environments"></a>Управление приложениями для использования в нескольких средах

Azure Service Fabric позволяет создавать кластеры, добавляя в них от одного до нескольких тысяч компьютеров. В большинстве случаев требуется развертывать приложение в нескольких конфигурациях кластеров: в локальном кластере разработки, в общем кластере разработки и в рабочем кластере. Все эти кластеры считаются разными средами, в которых должен выполняться код. Двоичные файлы приложения могут выполняться в этих средах без изменений, но часто требуется по-разному настроить приложение.

Рассмотрим два простых примера:
  - служба ожидает передачи данных через определенный порт, но это должны быть разные порты во всех средах;
  - необходимо указать разные учетные данные привязки для базы данных во всех средах.

## <a name="specifying-configuration"></a>Указание конфигурации

Предоставляемую конфигурацию можно разделить на две категории.

- Конфигурация, которая применяется к выполнению служб.
  - Например, номер порта конечной точки или число экземпляров службы.
  - Эта конфигурация задается в файле манифеста приложения или файле манифеста служб.
- Конфигурация, которая применяется к коду приложения.
  - Например, сведения о привязке для базы данных.
  - Эта конфигурация может предоставляться с помощью файлов конфигурации или переменных среды.

> [!NOTE]
> Не все атрибуты в файле манифеста приложения и файле манифеста служб поддерживают использование параметров.
> В таких случаях следует полагаться на замену строк в рабочем процессе развертывания. В Visual Studio Team Services можно воспользоваться таким расширением, как Replace Tokens: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens, а в Jenkins можно запустить задачу скрипта для замены значений.
>

## <a name="specifying-parameters-during-application-creation"></a>Указание параметров при создании приложения

При создании именованных экземпляров приложения в Service Fabric имеется возможность передавать параметры. Способ их передачи зависит от того, как создается экземпляр приложения.

  - В PowerShell командлет [`New-ServiceFabricApplication`](https://docs.microsoft.com/en-us/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) принимает параметры приложения в виде хэш-таблицы.
  - При использовании sfctl команда [`sfctl application create`](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) принимает параметры в виде строки JSON. Сценарий install.sh использует sfctl.
  - Visual Studio предоставляет набор файлов параметров в папке Parameters в проекте приложения. Эти файлы параметров используются при публикации из Visual Studio с помощью Visual Studio Team Service или Team Foundation Server. В Visual Studio файлы параметров передаются в сценарий Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Дополнительная информация
В приведенных ниже статьях описывается, как использовать некоторые основные понятия, приведенные в этой статье:

- [Как указать переменные среды для служб в Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Как указать номер порта службы с помощью параметров в Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Как параметризовать файлы конфигурации](service-fabric-how-to-parameterize-configuration-files.md)

- [Справочник по переменным среды](service-fabric-environment-variables-reference.md)
