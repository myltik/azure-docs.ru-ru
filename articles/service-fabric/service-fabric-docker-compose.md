---
title: "Docker Compose для Azure Service Fabric (предварительная версия)"
description: "Azure Service Fabric принимает формат Docker Compose для упрощения управления существующими контейнерами с помощью Service Fabric. Сейчас эта возможность доступна в предварительной версии."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 91b6e98df5df98bb557d7fac0475354322d95640
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---
# <a name="compose-application-support-in-service-fabric-preview"></a>Поддержка приложения Compose в Service Fabric (предварительная версия)

Средство Docker использует файл [docker-compose.yml](https://docs.docker.com/compose) для определения приложений с несколькими контейнерами.
Чтобы пользователи могли ознакомиться с инструментом Docker, который позволяет организовать существующие приложения-контейнеры в Service Fabric, мы изначально добавили в платформу поддержку предварительной версии Docker Compose. Платформа Service Fabric может принять минимум три файла `docker-compose.yml`. Так как эта возможность доступна в режиме предварительной версии, поддерживается только подмножество директив Compose. Например, обновления приложений не поддерживаются. Но вы всегда можете вместо обновления приложений удалить и развернуть их.

Чтобы использовать эту предварительную версию, необходимо создать кластер с помощью пакета SDK для предварительной версии (версии 255.255.x.x) на портале. 

> [!NOTE]
> Эта функция доступна в режиме предварительной версии и не поддерживается.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Развертывание файла Docker Compose в Service Fabric

Приведенные ниже команды создают приложение Service Fabric (в предыдущем примере оно называется `fabric:/TestContainerApp`), которое можно отслеживать и управлять им, как и любым приложением Service Fabric. Указанное имя приложения можно использовать для запросов работоспособности.

### <a name="using-powershell"></a>с использованием PowerShell.

Чтобы создать приложение Compose для Service Fabric из файла docker-compose.yml в PowerShell, выполните следующую команду.

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RepositoryUserName <>] [-RepositoryPassword <>] [-PasswordEnctypted]
```

`RepositoryUserName` и `RepoistoryPassword` задают имя пользователя и пароль реестра контейнеров. После завершения приложения для проверки его состояния можно использовать следующую команду.

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

Чтобы удалить приложение Compose с помощью PowerShell, используйте следующую команду.

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

### <a name="using-azure-cli-20"></a>Использование Azure CLI 2.0

Можно также выполнить следующую команду Azure CLI.

```azurecli
az sf compose create --application-id fabric:/TestContainerApp --compose-file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```

После создания приложения можно проверить его состояние с помощью следующей команды.

```azurecli
az sf compose status --application-id TestContainerApp [ --timeout ]
```

Чтобы удалить приложение Compose, выполните следующую команду.

```azurecli
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

## <a name="supported-compose-directives"></a>Поддерживаемые директивы Compose

В этой предварительной версии поддерживается подмножество параметров конфигурации из формата Compose версии 3. Поддерживаются следующие примитивы:

* Services->Deploy->replicas
* Services->Deploy->Placement->Constraints
* Services->Deploy->Resources->Limits
*         -cpu-shares
*         -memory
*         -memory-swap
* Services->Commands
* Services->Environment
* Services->Ports
* Services->image
* Services->Isolation (только для Windows)
* Services->logging->driver
* Services->logging->driver->options
* Volume & Deploy->Volume

Кластер должен быть настроен для принудительного применения ограничения ресурсов, как описано в статье об [управлении ресурсами](service-fabric-resource-governance.md). Другие директивы Docker Compose не поддерживаются в этой предварительной версии.

## <a name="servicednsname-computation"></a>Вычисление ServiceDnsName

Если в файле Compose указано полное доменное имя службы, то есть оно содержит точку (.), платформа Service Fabric зарегистрирует DNS-имя `<ServiceName>` с точкой. В противном случае каждый сегмент пути в имени ApplicationName становится меткой домена в DNS-имени службы. При этом первый сегмент пути становится меткой домена верхнего уровня.

Например, если в качестве имени приложения указано `fabric:/SampleApp/MyComposeApp`, то `<ServiceName>.MyComposeApp.SampleApp` будет зарегистрированным DNS-именем.

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Различия между моделью приложений Compose (определение экземпляра) и Service Fabric (определение типа)

Файл docker-compose.yml описывает набор контейнеров, доступных для развертывания, в том числе их свойства и конфигурации.
Например, файл может содержать переменные среды и порты. В файле docker-compose.yml также указываются параметры развертывания, такие как ограничения размещения, ограничения ресурсов и DNS-имена.

[Модель приложения Service Fabric](service-fabric-application-model.md) использует типы служб и типы приложений, в которых можно создать несколько экземпляров приложений того же типа. Например, можно создать по одному экземпляру приложения на клиента. Эта модель на основе типа поддерживает несколько версий одного типа приложений, зарегистрированных в среде выполнения.
Например, для клиента A может быть создан экземпляр приложения версии 1.0 типа AppTypeA, а для клиента Б может быть создан другой экземпляр приложения того же типа и версии. Типы приложений определяются в манифестах приложений, а параметры развертывания и имя приложения указываются во время создания приложения.

Хотя эта модель обеспечивает гибкость, мы также планируем поддерживать более простую модель развертывания на основе экземпляра, в которой типы будут извлекаться из файла манифеста. В этой модели каждое приложение получает собственный независимый манифест. Мы предварительно рассматриваем это действие, добавляя поддержку docker-compose.yml, в котором используется формат развертывания на основе экземпляра.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей [Моделирование приложения в структуре службы](service-fabric-application-model.md).

## <a name="related-articles"></a>Связанные статьи

* [Service Fabric и Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Приступая к работе с Service Fabric и XPlat CLI](service-fabric-azure-cli.md)

