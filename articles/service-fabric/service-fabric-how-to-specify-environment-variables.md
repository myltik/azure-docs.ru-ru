---
title: Как указать переменные среды для служб в Azure Service Fabric | Документация Майкрософт
description: Показано, как использовать переменные среды для приложений в Service Fabric.
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: 4325b3acd3cbc73ee5976021bebe96c267b2a6dd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206034"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Как указать переменные среды для служб в Service Fabric

В этой статье показано, как задать переменные среды для службы или контейнера в Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Процедуры указания переменных среды для служб

В этом примере задается значение переменной среды для контейнера. В статье предполагается, что у вас уже есть манифест приложения и манифест служб.

1. Откройте файл ServiceManifest.xml.
1. В элемент `CodePackage` добавьте новые элементы `EnvironmentVariables` и `EnvironmentVariable` для каждой переменной среды.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DeafultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    Переменные среды можно переопределить в манифесте приложения.

1. Для этого используйте элемент `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Дополнительная информация
Чтобы узнать больше о некоторых основных понятиях, рассмотренных в данной статье, ознакомьтесь с разделом [Управление параметрами приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md).

Сведения о других возможностях управления приложениями, доступными в Visual Studio, см. в статье [Использование Visual Studio для упрощения создания приложений Service Fabric и управления ими](service-fabric-manage-application-in-visual-studio.md).