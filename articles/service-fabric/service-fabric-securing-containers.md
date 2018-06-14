---
title: Импорт сертификатов в контейнер под управлением Azure Service Fabric | Документация Майкрософт
description: Узнайте, как импортировать файлы сертификатов в службу контейнеров Service Fabric.
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
ms.openlocfilehash: f234a6f6ca56d1833aac53f490feb5f667a6bf1b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208222"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Импорт файла сертификата в контейнер под управлением Service Fabric

Службы контейнеров можно защитить с помощью сертификата. Service Fabric предоставляет для служб в контейнере механизм, который обеспечивает доступ к сертификату, установленному на узлах кластера Windows или Linux (версии 5.7 или выше). Этот сертификат должен быть установлен в папку LocalMachine на всех узлах в кластере. Сведения о сертификате указываются в манифесте приложения после тега `ContainerHostPolicies`, как показано в следующем фрагменте кода:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

В кластерах Windows при запуске приложения в среде выполнения считываются сертификаты, создается PFX-файл и пароль для каждого сертификата. Доступ к PFX-файлу и паролю в контейнере можно получить с помощью следующих переменных среды: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

В кластерах Linux сертификаты (PEM) копируются из хранилища, заданного параметром X509StoreName, в контейнер. Ниже перечислены соответствующие переменные среды в Linux:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM;
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey.

Кроме того, если у вас уже есть сертификаты в требуемом формате и вам нужно обращаться к ним внутри контейнера, можно создать пакет данных внутри пакета приложения и указать в манифесте приложения следующее:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Импорт файлов сертификатов в контейнер выполняется с помощью службы контейнеров или процесса контейнера. Для импорта сертификата можно использовать сценарии `setupentrypoint.sh` или пользовательский исполняемый код в процессе контейнера. Ниже приведен пример кода C# для импорта PFX-файла:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Этот PFX-файл сертификата можно использовать для аутентификации приложения или службы, а также для безопасного обмена данными с другими службами. По умолчанию список управления доступом применяется к файлам только для SYSTEM. Вы можете применять список управления доступом к этим файлам для других учетных записей, если это нужно для работы службы.

Теперь ознакомьтесь со следующими статьями:

* [Развертывание контейнера Windows в Service Fabric на платформе Windows Server 2016](service-fabric-get-started-containers.md)
* [Развертывание контейнера Docker в Service Fabric на платформе Linux](service-fabric-get-started-containers-linux.md)
