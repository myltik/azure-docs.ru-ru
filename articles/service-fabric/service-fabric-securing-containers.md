---
title: "Безопасность контейнеров Azure Service Fabric | Документация Майкрософт"
description: "Узнайте, как защитить службы контейнеров."
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
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 7ebec89e1481ccc232403426c04bed0ffd6f4fe7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
# <a name="container-security"></a>Безопасность контейнера

Service Fabric предоставляет для служб в контейнере механизм, который обеспечивает доступ к сертификату, установленному на узлах кластера Windows или Linux (версии 5.7 или выше). Кроме того, Service Fabric поддерживает групповые управляемые учетные записи службы (gMSA) для контейнеров Windows. 

## <a name="certificate-management-for-containers"></a>Управление сертификатами для контейнеров

Службы контейнеров можно защитить с помощью сертификата. Этот сертификат должен быть установлен в папку LocalMachine на всех узлах в кластере. Сведения о сертификате указываются в манифесте приложения после тега `ContainerHostPolicies`, как показано в следующем фрагменте кода:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

В кластерах Windows при запуске приложения в среде выполнения считываются сертификаты, создается PFX-файл и пароль для каждого сертификата. Доступ к PFX-файлу и паролю в контейнере можно получить с помощью следующих переменных среды: 

* **Certificates_ServicePackageName_CodePackageName_CertName_PFX**
* **Certificates_ServicePackageName_CodePackageName_CertName_Password**

В кластерах Linux сертификаты (PEM) просто копируются из хранилища, заданного параметром X509StoreName, в контейнер. Ниже перечислены соответствующие переменные среды в Linux:

* **Certificates_ServicePackageName_CodePackageName_CertName_PEM**
* **Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey**

Кроме того, если у вас уже есть сертификаты требуемом формате и вам просто нужно обращаться к ним внутри контейнера, можно создать пакет данных внутри пакета приложения и указать в манифесте приложения следующее.

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
   <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Импорт файлов сертификатов в контейнер выполняется с помощью службы контейнеров или процесса контейнера. Для импорта сертификата можно использовать сценарии `setupentrypoint.sh` или пользовательский исполняемый код в процессе контейнера. Ниже приведен пример кода C# для импорта PFX-файла.

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


## <a name="set-up-gmsa-for-windows-containers"></a>Настройка gMSA для контейнеров Windows

Чтобы настроить gMSA, необходимо разместить файл спецификаций учетных данных (`credspec`) на всех узлах в кластере. Вы можете скопировать файл на все узлы, используя расширение виртуальной машины.  Файл `credspec` должен содержать информацию об учетной записи gMSA. Дополнительные сведения о файле `credspec` см. в статье [об учетных записях службы](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). Спецификация учетных данных и тег `Hostname` указаны в манифесте приложения. Тег `Hostname` должен соответствовать имени учетной записи gMSA, которая используется для запуска контейнера.  Тег `Hostname` позволяет контейнеру автоматически проходить проверку подлинности в других службах в домене с помощью проверки подлинности Kerberos.  В следующем фрагменте кода показан пример с указанием тегов `Hostname` и `credspec` в манифесте приложения.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>Дополнительная информация

* [Развертывание контейнера Windows в Service Fabric на платформе Windows Server 2016](service-fabric-get-started-containers.md)
* [Развертывание контейнера Docker в Service Fabric на платформе Linux](service-fabric-get-started-containers-linux.md)
