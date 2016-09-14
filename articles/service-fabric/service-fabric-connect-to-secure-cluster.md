<properties
   pageTitle="Аутентификация клиентского доступа к кластеру | Microsoft Azure"
   description="Описываются способы аутентификации клиентского доступа к кластеру Service Fabric с помощью сертификатов, а также безопасный обмен данными между клиентами и кластером."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# Безопасное подключение к кластеру
Когда клиент подключается к узлу кластера Service Fabric, он может пройти аутентификацию и установить безопасную связь с помощью безопасности на основе сертификатов. Это гарантирует, что только авторизованные пользователи могут получить доступ к кластеру для развертывания приложений и выполнения задач управления. Безопасность на основе сертификатов необходимо предварительно включить в кластере при его создании. Для обеспечения безопасности кластера следует использовать по крайней мере два сертификата: один для кластера и сервера, а второй для клиентского доступа. Рекомендуется также использовать дополнительные сертификаты и сертификаты клиентского доступа. Дополнительные сведения о сценариях обеспечения безопасности кластеров см. в разделе [Безопасность кластера](service-fabric-cluster-security.md).

Чтобы защитить обмен данными между клиентом и узлом кластера с помощью безопасности на основе сертификатов, необходимо сначала получить сертификат клиента и установить его в личное хранилище (Мое) на локальном компьютере или в личное хранилище текущего пользователя. Отпечаток в сертификате сервера также нужен, чтобы клиент мог аутентифицировать кластер.

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

<a id="connectsecurecluster"></a>
## Подключение к защищенному кластеру с помощью PowerShell

Для подключения к защищенному кластеру выполните следующую команду PowerShell: Сведения о сертификате должны соответствовать сертификату на узлах кластера.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

Например, приведенная выше команда PowerShell должна выглядеть следующим образом. *ServerCertThumbprint* — отпечаток сертификата сервера, установленного на узлах кластера, *FindValue* — отпечаток сертификата клиента администрирования.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```

## Подключение к защищенному кластеру с помощью интерфейсов API FabricClient
Ниже приведен [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). У узлов в кластере должны быть действительные сертификаты, общее имя или DNS-имя которых в сети SAN отображается в [свойстве RemoteCommonNames](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx), заданном для [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). Это обеспечивает взаимную аутентификацию между клиентом и узлом кластера.

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


## Дальнейшие действия

- [Обновление кластера Service Fabric](service-fabric-cluster-upgrade.md)
- [Управление приложениями Service Fabric в Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Общие сведения о модели работоспособности в Service Fabric](service-fabric-health-introduction.md)
- [Безопасность приложений и запуск от имени](service-fabric-application-runas-security.md)

<!---HONumber=AcomDC_0831_2016-->