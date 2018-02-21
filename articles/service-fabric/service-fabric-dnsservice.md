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
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 9871bc5aa4e74ab0faef401d67c4e9558eb5e14b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="dns-service-in-azure-service-fabric"></a>Служба DNS в Azure Service Fabric
Служба DNS является необязательной системной службой, которую можно включить в кластере для обнаружения других служб с помощью протокола DNS.

Так как многие службы, а именно контейнерные службы, могут содержать URL-адрес, возможность разрешить их с помощью стандартного протокола DNS (а не протокола службы именования) очень удобна, особенно в сценариях lift-and-shift. Служба DNS позволяет сопоставить DNS-имена с именем службы и, следовательно, разрешить IP-адреса конечных точек. 

Служба DNS сопоставляет DNS-имена с именами служб, которые, в свою очередь, разрешаются службой именования, чтобы вернуть конечную точку службы. DNS-имя службы предоставляется при создании. 

![Конечные точки службы][0]

## <a name="enabling-the-dns-service"></a>Включение службы DNS
Сначала необходимо включить службу DNS в кластере. Получите шаблон для кластера, который требуется развернуть. Можно использовать [примеры шаблонов](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) или создать шаблон Resource Manager. Службу DNS можно включить следующим образом:

1. Убедитесь, что версия `apiversion` ресурса `Microsoft.ServiceFabric/clusters` имеет значение `2017-07-01-preview`. А если не имеет, то обновите ее, как показано во фрагменте ниже.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Включите службу DNS, добавив следующий раздел `addonFeatures` после раздела `fabricSettings`, как показано во фрагменте кода ниже: 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. После обновления шаблона кластера примените указанные выше изменения и дождитесь завершения обновления. По завершении системная служба DNS начинает выполнение в кластере, который называется `fabric:/System/DnsService`, в разделе системной службы в обозревателе Service Fabric. 

Вы также можете включить службу DNS на портале во время создания кластера. Службу DNS можно включить, установив флажок `Include DNS service` в `Cluster configuration` меню, как показано на следующем снимке экрана:

![Включение службы DNS на портале][2]


## <a name="setting-the-dns-name-for-your-service"></a>Настройка DNS-имени для службы
Когда служба DNS уже выполняется в кластере, можно задать DNS-имена для служб. Это можно сделать декларативно для служб по умолчанию в файле `ApplicationManifest.xml` или с помощью команд Powershell.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Настройка DNS-имени службы по умолчанию в файле ApplicationManifest.xml
Откройте проект в Visual Studio или другом текстовом редакторе и откройте файл `ApplicationManifest.xml`. Перейдите к разделу служб по умолчанию и для каждой службы добавьте атрибут `ServiceDnsName`. В следующем примере показано, как установить для службы DNS-имя `service1.application1`.

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
После развертывания приложения в экземпляре службы в обозревателе Service Fabric отобразится DNS-имя для данного экземпляра, как показано на следующем изображении: 

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
При развертывании нескольких служб их конечные точки могут взаимодействовать с помощью DNS-имен. Служба DNS применяется только в службах без отслеживания состояния, так как DNS-протокол не может взаимодействовать со службами с отслеживанием состояния. Для служб с отслеживанием состояния можно использовать встроенный обратный прокси-сервер для HTTP-вызовов, чтобы вызывать определенный раздел службы.

Следующий код отображает порядок вызова другой службы, который является простым вызовом HTTP, где в качестве части URL-адреса указывается порт и любой дополнительный путь.

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

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о взаимодействии служб в кластере см. в статье [Подключение к службам в Service Fabric и взаимодействие с ними](service-fabric-connect-and-communicate-with-services.md).

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
