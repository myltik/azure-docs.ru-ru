---
title: Назначение политик доступа для конечных точек службы Service Fabric | Документация Майкрософт
description: Узнайте, как назначать политики безопасности доступа для конечных точек HTTP или HTTPS в службе Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: f9de8d213d11a8ccb3ffff484a67560d9e2abe77
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Назначение политики безопасности доступа для конечных точек HTTP и HTTPS
Если применяется политика запуска от имени и в манифесте службы объявляются ресурсы конечной точки HTTP, необходимо указать **SecurityAccessPolicy**.  **SecurityAccessPolicy** гарантирует, что к порты, выделенные для этих конечных точек, будут ограничены учетной записью пользователя, в которой выполняется служба. В противном случае файл **http.sys** не получит доступа к службе, а вызовы от клиента будут завершаться ошибками. В следующем примере учетная запись Customer1 применяется к конечной точке с именем **EndpointName**, предоставляя ей права полного доступа.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Для конечной точки HTTPS также укажите имя сертификата, который будет возвращен клиенту. Ссылки на сертификат добавляются с помощью **EndpointBindingPolicy**.  Сертификат определяется в разделе **Сертификаты** в манифесте приложения.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
На следующем этапе ознакомьтесь с указанными ниже статьями:
* [Сведения о модели приложения](service-fabric-application-model.md)
* [Указание ресурсов в манифесте службы](service-fabric-service-manifest-resources.md)
* [Развертывание приложения](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
