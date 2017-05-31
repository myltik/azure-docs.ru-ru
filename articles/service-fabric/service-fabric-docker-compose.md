---
title: "Средство Docker Compose для Azure Service Fabric (предварительная версия) | Документация Майкрософт"
description: "Платформа Azure Service Fabric принимает формат Docker Compose для упрощения управления существующими контейнерами с помощью Service Fabric. Сейчас эта возможность доступна в предварительной версии."
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
ms.date: 05/01/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 800f5bacd5197f64968fb1c169ef58330ee75e0d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017

---


# <a name="compose-application-support-in-service-fabric-preview"></a>Поддержка приложения Compose в Service Fabric (предварительная версия)

Средство Docker использует файл [docker-compose.yml](https://docs.docker.com/compose) для определения приложений с несколькими контейнерами. Чтобы пользователи могли ознакомиться со средством Docker, которое помогает организовывать существующие приложения-контейнеры в Service Fabric, мы изначально включили поддержку предварительной версии Docker Compose. Платформа Service Fabric может принять минимум три файла `docker-compose.yml`. Так как эта возможность доступна в режиме предварительной версии, поддерживается только подмножество директив Compose. Например, обновления приложений не поддерживаются. Но вы всегда можете вместо обновления приложений удалить и развернуть их.  

Чтобы использовать эту предварительную версию, необходимо установить предварительную версию пакета SDK (версии 255.255.x.x) на портале. 

> [!NOTE]
> Эта функция доступна в режиме предварительной версии и не поддерживается.

## <a name="using-a-docker-composeyml-file-with-service-fabric-preview"></a>Использование файла docker-compose.yml в Service Fabric (предварительная версия)

Чтобы создать приложение Service Fabric из файла docker-compose.yml в PowerShell, выполните следующую команду:

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RepositoryUserName <>] [-RepositoryPassword <>] [-PasswordEnctypted]
```

Параметры RepositoryUserName и RepoistoryPassword обозначают имя пользователя и пароль реестра контейнеров.

Если вы используете Azure CLI 2.0, выполните следующую команду:

```bash
az sf compose create --application-id fabric:/TestContainerApp --file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```
Эти команды создают приложение Service Fabric (в предыдущем примере оно называется `fabric:/TestContainerApp`), которое можно отслеживать с помощью Service Fabric Explorer. Параметр `ApplicationName` используется для запросов о работоспособности с помощью PowerShell, Azure CLI 2.0 или SFX.

Чтобы удалить приложение с помощью PowerShell, используйте следующую команду:

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

Чтобы удалить приложение с помощью Azure CLI 2.0, используйте следующую команду:

```bash
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

Чтобы получить состояние создания приложения, используйте следующую команду в PowerShell:

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

Для этой же цели в Azure CLI 2.0 используйте следующую команду:

```bash
az sf compose status --application-id TestContainerApp [ --timeout ]
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

Кластер должен быть настроен для принудительного применения ограничения ресурсов, как описано в статье об [управлении ресурсами](service-fabric-resource-governance.md).
Другие директивы Docker Compose не поддерживаются в этой предварительной версии. 

## <a name="servicednsname-computation"></a>Вычисление ServiceDnsName

Если в файле Compose указано полное доменное имя службы, то есть оно содержит точку (.), платформа Service Fabric зарегистрирует DNS-имя `<ServiceName>` с точкой. В противном случае каждый сегмент пути в имени ApplicationName становится меткой домена в DNS-имени службы. При этом первый сегмент пути становится меткой домена верхнего уровня. Таким образом, если в качестве имени приложения указано `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` будет зарегистрированным DNS-именем.

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Различия между моделью приложений Compose (определение экземпляра) и Service Fabric (определение типа)

Файл docker-compose.yml описывает набор контейнеров, доступных для развертывания, в том числе их свойства и настройки (например, переменные среды и порты). В файле docker-compose.yml также указываются параметры развертывания, такие как ограничения на размещение, ограничения ресурсов и DNS-имена.

[Модель приложений Service Fabric](service-fabric-application-model.md) использует типы служб и типы приложений, в которых можно создать несколько экземпляров приложений того же типа (например, один экземпляр приложения на одного клиента). Эта модель на основе типа поддерживает несколько версий одного типа приложений, зарегистрированных в среде выполнения. Например, для клиента A может быть создан экземпляр приложения версии 1.0 типа AppTypeA, а для клиента Б может быть создан другой экземпляр приложения того же типа и версии. Типы приложений определяются в манифестах приложений, а параметры развертывания и имя приложения указываются во время создания приложения.

Хотя эта модель обеспечивает гибкость, мы также планируем поддерживать более простую модель развертывания на основе экземпляров, в которой типы будут извлекаться из файла манифеста. В этой модели каждое приложение получает собственный независимый манифест. Мы предварительно рассматриваем это действие, добавляя поддержку docker-compose.yml, в котором используется формат развертывания на основе экземпляра.


## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей [Моделирование приложения в структуре службы](service-fabric-application-model.md).

