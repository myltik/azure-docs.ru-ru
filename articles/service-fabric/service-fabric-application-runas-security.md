---
title: Запуск службы Azure Service Fabric с использованием системных и локальных учетных записей безопасности | Документация Майкрософт
description: Узнайте о том, как запустить приложение Service Fabric с использованием системных и локальных учетных записей системы безопасности.  Создайте субъекты безопасности и примените политику запуска от имени для защищенного выполнения служб.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 3df5374911ee6381f25d08d23d565cdf8a7cd12f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Запуск службы с использованием локальной пользовательской или системной учетной записи
Платформа Azure Service Fabric помогает защищать приложения, работающие в кластере под разными учетными записями. По умолчанию приложения Service Fabric выполняются под учетной записью, используемой процессом Fabric.exe. Платформа также позволяет запускать приложения с использованием локальной пользовательской или системной учетной записи. Для этого нужно указать политики запуска от имени (RunAs) в манифесте приложения. Поддерживаются следующие типы учетных записей локальной системы: **LocalUser**, **NetworkService**, **LocalService** и **LocalSystem**.  При запуске Service Fabric в изолированном кластере Windows службу можно запустить с помощью [учетных записей домена Active Directory](service-fabric-run-service-as-ad-user-or-group.md) или [групповых управляемых учетных записей службы](service-fabric-run-service-as-gmsa.md).

Также можно определять и создавать группы пользователей, а затем добавлять в них пользователей для совместного управления. Это полезно, если для разных точек входа службы есть несколько пользователей и у них должны быть определенные общие права, доступные на уровне группы.

> [!NOTE] 
> Если применить политику запуска от имени к службе, манифест которой объявляет ресурсы конечной точки с протоколом HTTP, необходимо указать **SecurityAccessPolicy**.  Дополнительные сведения см. в статье [Назначение политики безопасности доступа для конечных точек HTTP и HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="create-local-user-groups"></a>Создание локальных групп пользователей
Вы можете определять и создавать группы пользователей, в которые можно добавить одного или нескольких пользователей. Это полезно, если для разных точек входа службы есть несколько пользователей и у них должны быть определены общие права, доступные на уровне группы. В следующем примере представлена локальная группа с именем **LocalAdminGroup** и привилегиями администратора. В нее входят два пользователя — Customer1 и Customer2 — в следующем примере манифеста приложения:

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

## <a name="create-local-users"></a>Создание локальных пользователей
Можно создать локального пользователя и использовать его для защиты службы в приложении. Если в разделе Principals манифеста приложения указан тип учетной записи **LocalUser** , платформа Service Fabric создает учетные записи локальных пользователей на компьютерах с развернутым приложением. По умолчанию эти учетные записи не соответствуют именам, указанным в манифесте приложения (например, Customer3 в следующем примере манифеста приложения). Вместо этого они создаются динамически и имеют случайные пароли.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Если для приложения требуется, чтобы учетная запись и пароль пользователя совпадали на всех компьютерах (например, чтобы включить аутентификацию NTLM), в манифесте кластера для параметра **NTLMAuthenticationEnabled** нужно задать значение true. Также нужно задать параметр **NTLMAuthenticationPasswordSecret**, который используется для создания одинакового пароля на всех компьютерах.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>Назначение политик пакетам кода службы
В разделе **RunAsPolicy** для **ServiceManifestImport** определяется учетная запись из раздела Principals, которая будет использоваться для выполнения пакета кода. Также это позволяет связать пакеты кода из манифеста службы с учетными записями пользователей в разделе Principals. Это можно сделать для точки входа Setup или Main. Также можно указать значение `All`, чтобы применить настройки к обеим точкам входа. Следующий пример демонстрирует применение разных политик.

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Если параметр **EntryPointType** не указан, по умолчанию используется `EntryPointType=”Main”`. Параметр **SetupEntryPoint** особенно полезен, если для установки нужно выполнить действия с расширенными правами от имени системной учетной записи. Дополнительные сведения см. в статье [Запуск скрипта запуска службы с использованием локальной или системной учетной записи](service-fabric-run-script-at-service-startup.md). Фактический код может выполняться под учетной записью с более низкими привилегиями.

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Применение политики по умолчанию ко всем пакетам кода службы
С помощью раздела **DefaultRunAsPolicy** можно указать учетную запись пользователя по умолчанию для всех пакетов кода, для которых не определен раздел **RunAsPolicy**. Иногда большую часть пакетов кода, указанных в используемых приложением манифестах служб, нужно запускать от имени одной и той же учетной записи пользователя. В таком случае в приложении можно определить стандартную политику запуска от имени этой учетной записи пользователя. Следующий пример устанавливает такие правила: если для пакета кода не указана политика **RunAsPolicy**, он будет запускаться под учетной записью **MyDefaultAccount**, указанной в разделе Principals.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Дополнительная информация
* [Сведения о модели приложения](service-fabric-application-model.md)
* [Указание ресурсов в манифесте службы](service-fabric-service-manifest-resources.md)
* [Развертывание приложения](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
