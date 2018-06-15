---
title: Развертывание Docker Compose для Azure Service Fabric (предварительная версия)
description: Azure Service Fabric принимает формат Docker Compose для упрощения управления существующими контейнерами с помощью Service Fabric. Сейчас эта возможность доступна в предварительной версии.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 43845a55589be9550e64b4a491b7d3675fb22e8c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641787"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Поддержка развертывания Docker Compose в Azure Service Fabric (предварительная версия)

Средство Docker использует файл [docker-compose.yml](https://docs.docker.com/compose) для определения приложений с несколькими контейнерами. Чтобы пользователи могли ознакомиться с инструментом Docker, который позволяет организовать существующие приложения-контейнеры в Azure Service Fabric, мы изначально добавили в платформу предварительную версию поддержки развертывания Docker Compose. Платформа Service Fabric может принять файлы `docker-compose.yml` версии 3 и более поздней. 

Так как эта поддержка доступна в режиме предварительной версии, поддерживается только подмножество директив Compose. Например, обновления приложений не поддерживаются. Но вы всегда можете вместо обновления приложений удалить и развернуть их.

Для использования этой предварительной версии создайте кластер со средой выполнения Service Fabric версии 5.7 (или более новой) с помощью портала Azure и соответствующего пакета SDK. 

> [!NOTE]
> Эта функция доступна в режиме предварительной версии и не поддерживается в рабочей среде.
> Приведенные ниже примеры основаны на среде выполнения версии 6.0 и пакете SDK версии 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Развертывание файла Docker Compose в Service Fabric

Приведенные ниже команды создают приложение Service Fabric (`fabric:/TestContainerApp`), которое можно отслеживать и которым можно управлять, как и любым приложением Service Fabric. Указанное имя приложения можно использовать для запросов работоспособности.
Service Fabric распознает DeploymentName как идентификатор развертывания Compose.

### <a name="use-powershell"></a>Использование PowerShell

Чтобы создать развертывание Compose для Service Fabric из файла docker-compose.yml, выполните следующую команду в PowerShell.

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` и `RegistryPassword` задают имя пользователя и пароль реестра контейнеров. После завершения развертывания можно проверить его состояние с помощью следующей команды.

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Чтобы удалить развертывание Compose с помощью PowerShell, используйте следующую команду.

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Чтобы запустить обновление развертывания Compose с помощью PowerShell, используйте следующую команду.

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

После согласия на обновление ход его выполнения можно отслеживать с помощью следующей команды.

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Использование интерфейса командной строки Service Fabric (sfctl)

Можно также выполнить следующую команду интерфейса командной строки Service Fabric.

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

После создания развертывания можно проверить его состояние с помощью следующей команды.

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Чтобы удалить развертывание Compose, выполните следующую команду.

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Чтобы начать обновление развертывания Compose, выполните следующую команду.

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

После согласия на обновление ход его выполнения можно отслеживать с помощью следующей команды.

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Поддерживаемые директивы Compose

В этой предварительной версии поддерживается подмножество параметров конфигурации из формата Compose версии 3. Поддерживаются следующие примитивы:

* Services > Deploy > Replicas
* Services > Deploy > Placement > Constraints
* Services > Deploy > Resources > Limits
    * -cpu-shares
    * -memory
    * -memory-swap
* Services > Commands
* Services > Environment
* Services > Ports
* Services > Image
* Services > Isolation (только для Windows)
* Services > Logging > Driver
* Services > Logging > Driver > Options
* Volume & Deploy > Volume

Настройте кластер для принудительного применения ограничения ресурсов, как описано в статье об [управлении ресурсами Service Fabric](service-fabric-resource-governance.md). Другие директивы Docker Compose не поддерживаются в этой предварительной версии.

## <a name="servicednsname-computation"></a>Вычисление ServiceDnsName

Если в файле Compose указано полное доменное имя службы (то есть оно содержит точку [.]), платформа Service Fabric зарегистрирует DNS-имя `<ServiceName>` с точкой. В противном случае каждый сегмент пути в имени приложения становится меткой домена в DNS-имени службы. При этом первый сегмент пути становится меткой домена верхнего уровня.

Например, если в качестве имени приложения указано `fabric:/SampleApp/MyComposeApp`, то `<ServiceName>.MyComposeApp.SampleApp` будет зарегистрированным DNS-именем.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Сравнение развертывания Compose (определение экземпляра) и модели приложения Service Fabric (определение типа)

Файл docker-compose.yml описывает набор контейнеров, доступных для развертывания, в том числе их свойства и конфигурации.
Например, файл может содержать переменные среды и порты. В файле docker-compose.yml также указываются параметры развертывания, такие как ограничения размещения, ограничения ресурсов и DNS-имена.

[Модель приложения Service Fabric](service-fabric-application-model.md) использует типы служб и типы приложений, в которых можно создать несколько экземпляров приложений того же типа. Например, можно создать по одному экземпляру приложения на клиента. Эта модель на основе типа поддерживает несколько версий одного типа приложений, зарегистрированных в среде выполнения.

Например, для клиента A может быть создан экземпляр приложения версии 1.0 типа AppTypeA, а для клиента Б может быть создан другой экземпляр приложения того же типа и версии. Типы приложений определяются в манифестах приложений, а параметры развертывания и имя приложения указываются во время создания приложения.

Хотя эта модель обеспечивает гибкость, мы также планируем поддерживать более простую модель развертывания на основе экземпляра, в которой типы будут извлекаться из файла манифеста. В этой модели каждое приложение получает собственный независимый манифест. Мы предварительно рассматриваем это действие, добавляя поддержку docker-compose.yml, в котором используется формат развертывания на основе экземпляра.

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со статьей [Моделирование приложения в структуре службы](service-fabric-application-model.md).
* [Azure Service Fabric command line](service-fabric-cli.md) (Интерфейс командной строки Azure Service Fabric)
