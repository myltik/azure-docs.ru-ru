---
title: "Отладка приложения Azure Service Fabric в Eclipse | Документация Майкрософт"
description: "Повысьте надежность и производительность служб, разрабатывая их в Eclipse и локальном кластере разработки."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: vturecek;mikhegn
ms.openlocfilehash: f3bcee3794de35005bd387ecfae7e6707f3cb5ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Отладка приложения Java Service Fabric с помощью Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio и CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse и Java](service-fabric-debugging-your-application-java.md)
> 

1. Запустите кластер локальной разработки, выполнив действия, описанные в статье [Настройка среды разработки Service Fabric](service-fabric-get-started-linux.md).

2. Обновите файл entryPoint.sh службы, которую необходимо отладить, таким образом, чтобы он запускал процесс Java с параметрами удаленной отладки. Этот файл можно найти в следующем расположении: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``. Для отладки в этом примере задается порт 8001.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Обновите манифест приложения, задав для отлаживаемой службы число экземпляров или реплик, равное 1. Эта настройка позволяет избежать конфликтов для порта, используемого для отладки. Например, для служб без отслеживания состояния, задайте ``InstanceCount="1"``, а для служб с отслеживанием состояния задайте целевые и минимальные размеры набора реплик, равные 1, следующим образом: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

4. Разверните приложения.

5. В интегрированной среде разработки Eclipse выберите **Запустить-> Debug Configurations (Конфигурации отладки)-> Remote Java Application and input connection properties (Свойства удаленного приложения Java и входных подключений)** и установите свойства следующим образом:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Установите точки останова на требуемых точках и запустите отладку приложения.

При сбое приложения можно также включить функцию coredumps. Выполните ``ulimit -c`` в оболочке. Если возвращается 0, то функция coredumps не включена. Чтобы функция coredumps работала без ограничений, выполните следующую команду: ``ulimit -c unlimited``. Состояние функции можно проверить с помощью команды ``ulimit -a``.  Если требуется обновить путь создания coredump, выполните следующую команду: ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``. 

### <a name="next-steps"></a>Дальнейшие действия

* [Сбор журналов с помощью системы диагностики Azure](service-fabric-diagnostics-how-to-setup-lad.md).
* [Мониторинг и диагностика состояния служб в локальной среде разработки](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
