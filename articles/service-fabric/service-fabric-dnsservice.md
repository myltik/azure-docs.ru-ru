---
title: Служба DNS в Azure Service Fabric | Документация Майкрософт
description: Используйте службу DNS Service Fabric для обнаружения микрослужб в кластере.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205000"
---
# <a name="dns-service-in-azure-service-fabric"></a>Служба DNS в Azure Service Fabric
Служба DNS является необязательной системной службой, которую можно включить в кластере для обнаружения других служб с помощью протокола DNS. 

Так как многие службы, а именно контейнерные службы, могут содержать URL-адрес, возможность разрешить их с помощью стандартного протокола DNS (а не протокола службы именования) очень удобна, особенно в сценариях lift-and-shift. Служба DNS позволяет сопоставить DNS-имена с именем службы и, следовательно, разрешить IP-адреса конечных точек. 

Служба DNS сопоставляет DNS-имена с именами служб, которые, в свою очередь, разрешаются службой именования, чтобы вернуть конечную точку службы. DNS-имя службы предоставляется при создании.

![Конечные точки службы](./media/service-fabric-dnsservice/dns.png)

Динамические порты не поддерживаются службой DNS. Чтобы разрешать службы, предоставляемые через динамические порты, используйте [службу обратного прокси-сервера](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Включение службы DNS
При создании кластера с помощью портала служба DNS включается по умолчанию с помощью флажка **Включить службу DNS** в меню **Конфигурация кластера**.

![Включение службы DNS на портале][2]

Если вы не используете портал для создания кластера или обновляете существующий кластер, необходимо включить службу DNS в шаблоне.

- Чтобы развернуть кластер, вы можете использовать [примеры шаблонов](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) или создать свой шаблон Resource Manager. 
- Чтобы обновить существующий кластер, можно перейти к его группе ресурсов на портале и щелкнуть **Сценарий автоматизации** для работы с шаблоном, который отражает текущее состояние кластера и других ресурсов в группе. Чтобы узнать больше, ознакомьтесь с разделом [Экспорт шаблона из группы ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group).

Получив шаблон, вы можете включить службу DNS следующим образом.

1. Убедитесь, что версия `apiversion` ресурса `Microsoft.ServiceFabric/clusters` имеет значение `2017-07-01-preview`. В противном случае обновите ее, как показано во фрагменте кода ниже.

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

3. После обновления шаблона кластера примените указанные выше изменения и дождитесь завершения обновления. По завершении обновления в кластере запустится системная служба DNS. Имя этой службы — `fabric:/System/DnsService`, и ее можно найти в разделе служб **Система** в обозревателе Service Fabric. 


## <a name="setting-the-dns-name-for-your-service"></a>Настройка DNS-имени для службы
Когда служба DNS уже выполняется в кластере, можно задать DNS-имена для служб. Это можно сделать декларативно для служб по умолчанию в файле `ApplicationManifest.xml` или с помощью команд PowerShell.

DNS-имя службы разрешается в кластере. Настоятельно рекомендуется использовать схему именования `<ServiceDnsName>.<AppInstanceName>`. Пример: `service1.application1`. Это гарантирует уникальность DNS-имени в кластере. Если приложение развернуто с помощью Docker Compose, службам автоматически присваиваются DNS-имена с помощью данной схемы именования.

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
При развертывании нескольких служб их конечные точки могут взаимодействовать с помощью DNS-имен. Служба DNS применяется только в службах без отслеживания состояния, так как DNS-протокол не может взаимодействовать со службами с отслеживанием состояния. Для служб с отслеживанием состояния можно использовать встроенную [службу обратного прокси-сервера](./service-fabric-reverseproxy.md) для HTTP-вызовов, чтобы вызывать определенную секцию службы. Динамические порты не поддерживаются службой DNS. Чтобы разрешать службы, использующие динамические порты, можно использовать обратный прокси-сервер.

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
