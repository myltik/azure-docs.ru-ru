<properties
   pageTitle="Защита трафика при репликации служб с отслеживанием состояния в Service Fabric (Azure)."
   description="Когда репликация активирована, службы с отслеживанием состояния реплицируют свое состояние с первичных реплик во вторичные. Связанный с репликацией трафик необходимо защитить от перехвата и изменения."
   services="service-fabric"
   documentationCenter=".net"
   authors="leikong"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="leikong"/>

# Защита трафика репликации

Когда репликация активирована, службы с отслеживанием состояния реплицируют свое состояние по всем репликам. Эта статья посвящена вопросу защиты трафика репликации.

Существует два типа поддерживаемых параметров безопасности:

- X509. Для защиты канала связи используется сертификат X509. Пользователи должны добавить в список управления доступом (ACL) закрытые ключи сертификатов, чтобы разрешить выполнение хост-процессов субъекта и службы. Это позволит использовать учетные данные сертификатов.
- Windows. Для защиты канала связи используется система безопасности Windows (требуется служба Active Directory).

В следующем разделе рассказывается, как настроить эти два типа параметров. То, какой тип используется, определено в конфигурации CredentialType.

## CredentialType=X509

### Имена конфигураций

|Имя|Примечания|
|----|-------|
|StoreLocation|Расположение хранилища, в котором следует искать сертификат: CurrentUser или LocalMachine.|
|StoreName|Имя хранилища, в котором следует искать сертификат.|
|FindType|Определяет тип значения, указанного в параметре FindValue: FindBySubjectName или FindByThumbPrint.|
|FindValue|Искомое значение, используемое для поиска сертификата.|
|AllowedCommonNames|Разделенный запятыми список общих имен сертификатов и DNS-имен, используемых репликаторами.|
|IssuerThumbprints|Разделенный запятыми список отпечатков издателя сертификата. В случае использования этой конфигурации входящий сертификат считается действительным, если его издатель указан в этом списке. Проверка цепочки сертификатов выполняется всегда.|
|RemoteCertThumbprints|Разделенный запятыми список отпечатков сертификатов, используемых репликаторами.|
|ProtectionLevel|Указывает на метод защиты данных: подписание (Sign), шифрование и подписание (EncryptAndSign), без защиты (None).|

## CredentialType=Windows

### Имена конфигураций

|Имя|Примечания|
|----|-------|
|ServicePrincipalName|Зарегистрированное для службы имя субъекта-службы. Конфигурация может быть пустой, если хост-процессы субъекта и службы запускаются под учетной записью компьютера (например, NetworkService, LocalSystem и т. д.).|
|WindowsIdentities|Разделенный запятыми список удостоверений Windows для всех хост-процессов службы и субъекта.
|ProtectionLevel|Указывает на метод защиты данных: подписание (Sign), шифрование и подписание (EncryptAndSign), без защиты (None).|

## Примеры

### Пример 1. CredentialType=X509

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="X509" />
  <Parameter Name="FindType" Value="FindByThumbprint" />
  <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
  <Parameter Name="StoreLocation" Value="LocalMachine" />
  <Parameter Name="StoreName" Value="My" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
  <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
</Section>
```

### Пример 2. CredentialType=Windows
В этом фрагменте кода показан пример, когда все хост-процессы службы и субъекта запускаются под учетной записью NetworkService или LocalSystem. Для параметра ServicePrincipalName не указано значение.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="" />
  <!--This machine group contains all machines in a cluster-->
  <Parameter Name="WindowsIdentities" Value="redmond\ClusterMachineGroup" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```

### Пример 3. CredentialType=Windows
В этом фрагменте кода показан пример, когда все хост-процессы службы и субъекта запускаются под групповой управляемой учетной записью службы (в параметре ServicePrincipalName должно быть задано допустимое имя субъекта-службы).

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="servicefabric/cluster.microsoft.com" />
  <--All actor/service host processes run as redmond\GroupManagedServiceAccount-->
  <Parameter Name="WindowsIdentities" Value="redmond\GroupManagedServiceAccount" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```
 

<!---HONumber=July15_HO4-->