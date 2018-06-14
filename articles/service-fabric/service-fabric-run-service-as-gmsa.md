---
title: Запуск службы Azure Service Fabric в групповой управляемой учетной записи службы | Документы Microsoft
description: Узнайте, как запускать службу в групповой управляемой учетной записи службы в автономном кластере Service Fabric под управлением Windows.
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
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 5f93285061708172b9b6ac40dc97fce08f7b2a86
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206719"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Запуск службы в групповой управляемой учетной записи службы
В автономном кластере Windows Server вы можете запустить службу как групповую управляемую учетную запись службы (gMSA) с помощью политики запуска от имени.  По умолчанию приложения Service Fabric выполняются под учетной записью, используемой процессом Fabric.exe. Запуск приложений в разных учетных записях позволяет изолировать друг от друга выполняемые приложения, даже если они запущены в общей среде. Обратите внимание, что в вашем домене используется локальная служба Active Directory, а не Azure Active Directory (Azure AD). При использовании групповой управляемой учетной записи службы в манифесте приложения не сохраняется ни обычный, ни зашифрованный пароль.  Кроме того, можно запустить службу как [группу или пользователя Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

В следующем примере показано, как создать групповую управляемую учетную запись службы *svc-Test$*, развернуть эту учетную запись на узлах кластера и настроить субъект-пользователь.

Предварительные требования:
- Домену нужен корневой ключ KDS.
- Домен должен иметь функциональный уровень Windows Server 2012 или выше.

1. Попросите администратора домена Active Directory создать групповую управляемую учетную запись службы с помощью командлета `New-ADServiceAccount` и убедитесь, что `PrincipalsAllowedToRetrieveManagedPassword` включает в себя все узлы кластера Service Fabric. Значения `AccountName`, `DnsHostName` и `ServicePrincipalName` должны быть уникальными.

    ```poweshell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Установите и протестируйте групповую управляемую учетную запись на каждом из узлов кластера Service Fabric (например, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`).
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Настройте субъекта-пользователя, а также тег RunAsPolicy, чтобы он указывал на этого пользователя.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> Если применить политику запуска от имени к службе, манифест которой объявляет ресурсы конечной точки с протоколом HTTP, необходимо указать **SecurityAccessPolicy**.  Дополнительные сведения см. в статье [Назначение политики безопасности доступа для конечных точек HTTP и HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Теперь ознакомьтесь со следующими статьями:
* [Сведения о модели приложения](service-fabric-application-model.md)
* [Указание ресурсов в манифесте службы](service-fabric-service-manifest-resources.md)
* [Развертывание приложения](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
