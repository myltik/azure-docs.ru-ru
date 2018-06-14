---
title: Как контейнеризовать микрослужбы Azure Service Fabric (предварительная версия)
description: К платформе Azure Service Fabric добавлены новые функции для контейнеризации микрослужб Service Fabric. Эта функция в настоящее время находится на стадии предварительной версии.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.openlocfilehash: 3741e74e70769d186da2757b43ca60bbb1e78a1f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212659"
---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Как контейнеризовать Azure Service Fabric Reliable Services и Reliable Actors (предварительная версия)

Service Fabric поддерживает контейнеризацию микрослужб Service Fabric (службы на основе Reliable Services и Reliable Actors). Дополнительные сведения см. в статье [Service Fabric и контейнеры](service-fabric-containers-overview.md).

Это предварительная версия функции. В этой статье объясняется, как получить службу, работающую в контейнере.  

> [!NOTE]
> Эта функция доступна в режиме предварительной версии и не поддерживается в рабочей среде. Сейчас эта функция работает только в Windows. Чтобы запустить контейнеры, кластер должен работать на компьютере под управлением Windows Server 2016 с контейнерами.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Инструкции по контейнеризации приложения Service Fabric

1. Откройте приложение Service Fabric в Visual Studio.

2. Добавьте класс [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) в проект. Код в этом классе является вспомогательным приложением для правильной загрузки двоичных файлов среды выполнения Service Fabric в приложение, которое работает в контейнере.

3. Каждый пакет кода, который вы бы хотели контейнеризовать, инициализирует загрузчик на точке входа программы. Добавьте статический конструктор, показанный в следующем фрагменте кода, в файл точки входа программы.

  ```csharp
  namespace MyApplication
  {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
  ```

4. Выполните сборку проекта и [упакуйте](service-fabric-package-apps.md#Package-App) его. Чтобы выполнить сборку проекта и создать пакет, щелкните правой кнопкой проект приложения в обозревателе решений и выберите команду **Пакет**.

5. Для каждого пакета кода, который нужно контейнеризовать, запустите скрипт PowerShell [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Оно используется следующим образом.
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  Скрипт создает папку с артефактами Docker в папке $dockerPackageOutputDirectoryPath. Измените созданный файл Dockerfile, чтобы открыть порты, запустить скрипты установки и при необходимости выполнить другие действия.

6. Теперь [выполните сборку](service-fabric-get-started-containers.md#Build-Containers) пакета контейнера Docker и [отправьте](service-fabric-get-started-containers.md#Push-Containers) его в репозиторий.

7. Измените файлы ApplicationManifest.xml и ServiceManifest.xml, чтобы добавить образ контейнера, сведения о репозитории, проверку подлинности реестра и сопоставление портов с узлами. Сведения об изменении манифестов см. в статье [Создание первого контейнера-приложения Service Fabric в Windows](service-fabric-get-started-containers.md). Определение пакета кода в манифесте службы необходимо заменить соответствующим образом контейнера. Обязательно замените тип EntryPoint типом ContainerHost.

  ```xml
<!-- Code package is your service executable. -->
<CodePackage Name="Code" Version="1.0.0">
  <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
  </EntryPoint>
  <!-- Pass environment variables to your container: -->    
</CodePackage>
  ```

8. Добавьте сопоставление портов с узлами для репликатора и конечной точки службы. Так как оба эти порта назначены в среде выполнения платформой Service Fabric, для ContainerPort устанавливается значение нуль, чтобы использовать порт, назначенный для сопоставления.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. Чтобы протестировать это приложение, необходимо развернуть его в кластере под управлением версии 5.7 или более поздней. Кроме того, необходимо изменить и обновить параметры кластера, чтобы включить эту предварительную версию функции. Выполните инструкции из этой [статьи](service-fabric-cluster-fabric-settings.md), чтобы добавить параметр, показанный далее.
```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
```
10. Затем [разверните](service-fabric-deploy-remove-applications.md) измененный пакет приложения в этом кластере.

Теперь в вашем кластере работает контейнерное приложение Service Fabric.

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о запуске [контейнеров в Service Fabric](service-fabric-get-started-containers.md).
* Дополнительные сведения о [жизненном цикле приложения](service-fabric-application-lifecycle.md) Service Fabric.
