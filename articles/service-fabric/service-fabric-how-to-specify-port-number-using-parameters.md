---
title: Как указать номер порта службы с помощью параметров в Azure Service Fabric | Документация Майкрософт
description: Показано, как использовать параметры, чтобы задать порт для приложения в Service Fabric.
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
ms.openlocfilehash: 06cfb375c6c18082a0d0316cfcb742a7779fc8a8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206384"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Как указать номер порта службы с помощью параметров в Service Fabric

В этой статье показано, как указать номер порта службы с помощью параметров в Service Fabric, используя Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Процедура указания номера порта службы с помощью параметров

В этом примере задается номер порта веб-API ASP.NET Core с помощью параметра.

1. Откройте Visual Studio и создайте приложение Service Fabric.
1. Выберите шаблон "ASP.NET Core без отслеживания состояния".
1. Выберите "Веб-API".
1. Откройте файл ServiceManifest.xml.
1. Обратите внимание на имя конечной точки, указанное для службы. Значение по умолчанию — `ServiceEndpoint`.
1. Откройте файл ApplicationManifest.xml.
1. В элемент `ServiceManifestImport` добавьте новый элемент `RessourceOverrides` со ссылкой на конечную точку в файле ServiceManifest.xml.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Теперь в элементе `Endpoint` можно с помощью параметра переопределить любой атрибут. В этом примере указывается атрибут `Port`, и ему присваивается значение — имя параметра в квадратных скобках, например `[MyWebAPI_PortNumber]`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Затем в файле ApplicationManifest.xml можно указать этот параметр в элементе `Parameters`.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. После этого определите `DefaultValue`.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Откройте папку ApplicationParameters и откройте файл `Cloud.xml`.
1. Чтобы указать другой порт для публикации на удаленный кластер, добавьте в этот файл параметр с номером порта.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="80" />
      </Parameters>
    ```

При публикации приложения из Visual Studio с помощью профиля публикации Cloud.xml служба будет использовать порт 80. Если приложение развертывается без указания параметра MyWebAPI_PortNumber, то служба использует порт 8080.

## <a name="next-steps"></a>Дополнительная информация
Чтобы узнать больше о некоторых основных понятиях, рассмотренных в данной статье, ознакомьтесь с разделом [Управление параметрами приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md).

Сведения о других возможностях управления приложениями, доступными в Visual Studio, см. в статье [Использование Visual Studio для упрощения создания приложений Service Fabric и управления ими](service-fabric-manage-application-in-visual-studio.md).