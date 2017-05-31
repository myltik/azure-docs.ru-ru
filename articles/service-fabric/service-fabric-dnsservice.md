---
title: "Служба DNS в Azure Service Fabric | Документация Майкрософт"
description: "Используйте службу DNS Service Fabric для обнаружения микрослужб в кластере."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e0f6a3a91207b73320d60a498d635262ef730d89
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="dns-service-in-azure-service-fabric"></a>Служба DNS в Azure Service Fabric
Служба DNS является необязательной системной службой, которую можно включить в кластере для обнаружения других служб с помощью протокола DNS.

Так как многие службы, а именно контейнерные службы, могут содержать URL-адрес, возможность разрешить его с помощью стандартного протокола DNS (а не протокола службы именования) очень удобна, особенно в сценариях Lift-аnd-Shift приложения. Служба DNS позволяет сопоставить DNS-имена с именем службы и, следовательно, разрешить IP-адреса конечных точек. 

Как показано на схеме ниже, служба DNS, выполняющаяся в кластере Service Fabric, сопоставляет DNS-имена с именами служб, которые затем разрешаются службой именования, чтобы вернуть адреса конечных точек для подключения. DNS-имя службы предоставляется при создании. 

![Конечные точки службы][0]

## <a name="enabling-the-dns-service"></a>Включение службы DNS
Сначала необходимо включить службу DNS в кластере. Получите шаблон для кластера, который требуется развернуть. Можно использовать [примеры шаблонов](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) или создать шаблон Resource Manager. Службу DNS можно включить следующим образом:

1. Сначала убедитесь, что `apiversion` ресурса `Microsoft.ServiceFabric/clusters` имеет значение `2017-07-01-preview`, как показано в следующем фрагменте. Если оно отличается, то необходимо задать для `apiVersion` значение `2017-07-01-preview`.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Включите службу DNS, добавив следующий раздел `addonFeatures` после раздела `fabricSettings`, как показано ниже.

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. После обновления шаблона кластера примените изменения и дождитесь завершения обновления. По завершении вы увидите, что системная служба DNS выполняется в кластере, который называется `fabric:/System/DnsService`, в разделе системной службы в обозревателе Service Fabric. 

## <a name="setting-the-dns-name-for-your-service"></a>Настройка DNS-имени для службы
Теперь, когда служба DNS работает в кластере, можно задать DNS-имена для служб. Это можно сделать декларативно для служб по умолчанию в `ApplicationManifest.xml` или с помощью PowerShell.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Настройка DNS-имени службы по умолчанию в файле ApplicationManifest.xml
Откройте проект в Visual Studio или другом текстовом редакторе и откройте файл `ApplicationManifest.xml`. Перейдите к разделу служб по умолчанию и для каждой службы добавьте атрибут `ServiceDnsName`. В следующем примере показано, как установить для службы DNS-имя `service1.application1`.

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Теперь разверните приложение. После развертывания приложения перейдите к экземпляру службы в обозревателе Service Fabric, и вы увидите DNS-имя для данного экземпляра, как показано ниже. 

![Конечные точки службы][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Задание DNS-имени службы с помощью PowerShell
DNS-имя службы можно задать при ее создании с помощью команды PowerShell `New-ServiceFabricService`. В следующем примере создается новая служба без отслеживания состояния с DNS-именем `service1.application1`.

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>Использование DNS в службах
При развертывании нескольких служб их конечные точки могут взаимодействовать с помощью DNS-имен. Это относится только к службам без отслеживания состояния, так как DNS-протокол не знает, кто должен взаимодействовать со службами с отслеживанием состояния. Для служб с отслеживанием состояния можно использовать встроенные обратный прокси-сервер для HTTP-вызовов, чтобы вызывать определенный раздел службы.

Следующий код отображает порядок вызова другой службы, который является простым вызовом HTTP. Обратите внимание, что в качестве части URL-адреса необходимо указать порт и любой дополнительный путь.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о взаимодействии служб в кластере см. в статье [Подключение к службам в Service Fabric и взаимодействие с ними](service-fabric-connect-and-communicate-with-services.md).

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG

