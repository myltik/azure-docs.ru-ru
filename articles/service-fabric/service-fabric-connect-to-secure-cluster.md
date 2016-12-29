---
title: "Проверка подлинности клиентского доступа к кластеру | Документация Майкрософт"
description: "Сведения о способах проверки подлинности клиентского доступа к кластеру Service Fabric, а также об обеспечении безопасного обмена данными между клиентами и кластером."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 4ba0f864dc28beebb80567d3fac7f12cc42df677
ms.openlocfilehash: 09557bd9c83318b2bdff8ecdefedc141eb7f80db


---
# <a name="connect-to-a-secure-cluster"></a>Безопасное подключение к кластеру
Когда клиент подключается к узлу кластера Service Fabric, он может пройти проверку подлинности и установить безопасную связь на основе сертификатов или с помощью Azure Active Directory (AAD). Такая проверка подлинности гарантирует, что только авторизованные пользователи могут получить доступ к кластеру и развернутым приложениям для выполнения задач управления.  Конфигурацию обеспечения безопасности на основе сертификатов или AAD необходимо предварительно включить в кластере при его создании.  Дополнительные сведения о сценариях обеспечения безопасности кластеров см. в разделе [Безопасность кластера](service-fabric-cluster-security.md). Если безопасность в кластере обеспечивается сертификатами, на компьютере, который подключается к нему, необходимо [настроить сертификат клиента](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert).

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-cli"></a>Подключение к защищенному кластеру с помощью Azure CLI
Следующие команды интерфейса командной строки Azure позволяют подключиться к безопасному кластеру. 

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Подключение к защищенному кластеру с использованием сертификата клиента
Сведения о сертификате должны соответствовать сертификату на узлах кластера. 

Если ваш сертификат выдан центром сертификации (ЦС), добавьте параметр `--ca-cert-path` , как показано в следующем примере. 

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Если вы используете несколько центров сертификации, используйте в качестве разделителя запятые. 

Если общее имя в сертификате не соответствует конечной точке подключения, для обхода проверки можно использовать параметр `--strict-ssl-false` . 

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```

Если вы хотите пропустить проверку ЦС, добавьте параметр ``--reject-unauthorized-false`` , как показано в следующем примере.

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

Чтобы подключиться к кластеру, защищенному с помощью самозаверяющего сертификата, удалите проверку ЦС и проверку общего имени с помощью следующей команды:

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

После подключения вы сможете взаимодействовать с кластером с помощью дополнительных команд интерфейса командной строки. 

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-secure-cluster-using-powershell"></a>Подключение к защищенному кластеру с помощью PowerShell
Перед выполнением операций в кластере с помощью PowerShell к этому кластеру сначала нужно подключиться. Это подключение используется для выполнения всех последующих команд в течение определенного сеанса PowerShell.

### <a name="connect-to-an-unsecure-cluster"></a>Подключение к незащищенному кластеру

Чтобы подключиться к незащищенному кластеру, добавьте в следующую команду **Connect-ServiceFabricCluster** адрес конечной точки кластера.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Подключение к защищенному кластеру с помощью Azure Active Directory

Чтобы подключиться к защищенному кластеру, использующему Azure Active Directory для проверки подлинности административного доступа, предоставьте отпечаток сертификата кластера и используйте флаг *AzureActiveDirectory*.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Подключение к защищенному кластеру с использованием сертификата клиента
Чтобы подключиться к защищенному кластеру, использующему сертификаты клиента для проверки подлинности административного доступа, выполните следующую команду PowerShell. Предоставьте отпечаток сертификата кластера, а также отпечаток сертификата клиента с разрешениями на управление кластером. Сведения о сертификате должны соответствовать сертификату на узлах кластера.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* — отпечаток сертификата сервера, установленного на узлах кластера. *FindValue* — отпечаток сертификата клиента администрирования.
Когда все параметры указаны, команда выглядит так: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```


<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-secure-cluster-using-the-fabricclient-apis"></a>Подключение к защищенному кластеру с помощью интерфейсов API FabricClient
В пакете SDK для Service Fabric предусмотрен класс [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) для управления кластером. 

### <a name="connect-to-an-unsecure-cluster"></a>Подключение к незащищенному кластеру

Чтобы подключиться к удаленному незащищенному кластеру, создайте экземпляр FabricClient и укажите адрес кластера.

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

В коде, выполняемом в кластере (например, в надежной службе), создайте класс FabricClient, *не* указывая адрес кластера. Класс FabricClient подключается к локальному шлюзу управления на узле, где в настоящее время выполняется код, без дополнительного перехода по сети.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Подключение к защищенному кластеру с использованием сертификата клиента

У узлов в кластере должны быть действительные сертификаты, общее имя или DNS-имя которых в сети SAN отображается в [свойстве RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx), заданном для [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Это обеспечивает взаимную проверку подлинности между клиентом и узлами кластера.

```csharp
string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
FabricClient fc = new FabricClient(xc, connection);
Task<bool> t = fc.PropertyManager.NameExistsAsync(new Uri("fabric:/any"));
try
{
    bool result = t.Result;
    Console.WriteLine("Cluster is connected");
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();

    // Client certificate
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;

    // Server certificate
    xc.RemoteCertThumbprints.Add(thumb);
    xc.RemoteCommonNames.Add(name);

    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Подключение к защищенному кластеру с помощью Azure Active Directory

Следующая процедура позволяет использовать Azure Active Directory в качестве удостоверения клиента, а сертификат сервера — в качестве удостоверения сервера.

Чтобы перейти в интерактивный режим, в котором всплывает интерактивное диалоговое окно входа в AAD, выполните следующую команду:

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

FabricClient fc = new FabricClient(
    claimsCredentials,
    connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

Чтобы перейти в автоматический режим без участия пользователя, выполните команду ниже.

(Этот пример основан на пространстве имен Microsoft.IdentityModel.Clients.ActiveDirectory версии 2.19.208020213.

Дополнительные сведения о получении маркера и самом пространстве имен см. в справочной документации по [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).)

```csharp
string tenantId = "c15cfcea-02c1-40dc-8466-fbd0ee0b05d2";
string clientApplicationId = "118473c2-7619-46e3-a8e4-6da8d5f56e12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob"
    );

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";
ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

FabricClient fc = new FabricClient(
   claimsCredentials,
   connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    string token = "";
    try
    {
        var authResult = authContext.AcquireToken(
            resource,
            clientId,
            new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
        token = authResult.AccessToken;
    }
    catch (AdalException ex)
    {
        Console.WriteLine("Get AccessToken failed: {0}", ex.Message);
    }

    return token;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Подключение к защищенному кластеру с помощью Service Fabric Explorer
Чтобы подключиться к [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) для заданного кластера, просто перейдите в браузере по адресу:

`http://<your-cluster-endpoint>:19080/Explorer`

Полный URL-адрес доступен также на панели основных компонентов кластера портала Azure.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Подключение к защищенному кластеру с помощью Azure Active Directory

Чтобы подключиться к кластеру, защищенному с помощью AAD, перейдите в браузере по адресу:

`https://<your-cluster-endpoint>:19080/Explorer`

После этого автоматически появится диалоговое окно с запросом на ввод учетных данных AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Подключение к защищенному кластеру с использованием сертификата клиента

Чтобы подключиться к кластеру, защищенному с использованием сертификатов, перейдите в браузере по адресу:

`https://<your-cluster-endpoint>:19080/Explorer`

После этого автоматически появится запрос на выбор сертификата клиента.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Настройка сертификата клиента на удаленном компьютере
Для обеспечения безопасности кластера следует использовать по крайней мере два сертификата: один для кластера и сервера, а второй для клиентского доступа.  Рекомендуется также использовать дополнительные сертификаты и сертификаты клиентского доступа.  Чтобы защитить обмен данными между клиентом и узлом кластера с помощью сертификатов, сначала необходимо получить и установить сертификат клиента. Его можно установить в личное хранилище на локальном компьютере или в личное хранилище текущего пользователя.  Отпечаток в сертификате сервера также нужен, чтобы клиент мог аутентифицировать кластер.

Выполните следующий командлет PowerShell, чтобы установить сертификат клиента на компьютер, который используется для доступа к кластеру.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Если это самозаверяющий сертификат, то перед использованием для подключения к безопасному кластеру его необходимо импортировать в хранилище "Доверенные лица" на своем компьютере.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>Дальнейшие действия
* [Обновление кластера Service Fabric](service-fabric-cluster-upgrade.md)
* [Управление приложениями Service Fabric в Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Общие сведения о модели работоспособности в Service Fabric](service-fabric-health-introduction.md)
* [Безопасность приложений и запуск от имени](service-fabric-application-runas-security.md)




<!--HONumber=Nov16_HO4-->


