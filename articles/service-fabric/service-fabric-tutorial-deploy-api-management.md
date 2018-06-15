---
title: Интеграция Azure Service Fabric со службой управления API | Документация Майкрософт
description: В этом руководстве вы узнаете, как быстро приступить к работе со службой управления API Azure и Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: f209e992c4562f11727613c58e1e94483af03bb7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599260"
---
# <a name="tutorial-deploy-api-management-with-service-fabric"></a>Руководство. Развертывание службы управления API с помощью Service Fabric
Это руководство представляет собой четвертую часть цикла.  Расширенный сценарий развертывания для службы управления API Azure в Service Fabric.  Управление API позволяет публиковать API-интерфейсы с широким набором правил маршрутизации для служб серверной части Service Fabric. Обычно, облачным приложениям требуется интерфейсный шлюз, который предоставляет единую точку передачи входящего трафика пользователей, устройств или других приложений. В Service Fabric шлюзом может быть любая служба без отслеживания состояния, предназначенная для обработки входящего трафика, например приложение ASP.NET Core, концентраторы событий, Центр Интернета вещей или служба управления API Azure. 

В нем показано, как настроить [службу управления API Azure](../api-management/api-management-key-concepts.md) с помощью Service Fabric для перенаправления трафика во внутреннюю службу в Service Fabric.  Выполнив инструкции из этого руководства, вы развернете службу управления API в виртуальной сети и настроите API для отправки трафика во внутренние службы без отслеживания состояния. Дополнительные сведения о сценариях службы управления API Azure и Service Fabric см. в [обзорной статье](service-fabric-api-management-overview.md).

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Развертывание управления API
> * Настройка управления API
> * Создание операции API
> * Настройка внутренней политики
> * Добавление API в продукт

Из этого цикла руководств вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * создание защищенного [кластера Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) или [кластера Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) в Azure;
> * [свертывание и развертывание кластера](service-fabric-tutorial-scale-cluster.md);
> * [обновление среды выполнения кластера;](service-fabric-tutorial-upgrade-cluster.md)
> * развертывание службы управления API с помощью Service Fabric.

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [модуль Azure PowerShell версии 4.1 или более поздней версии](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) либо [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Создайте защищенный [кластер Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) или [кластер Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) в Azure.
- Если вы развертываете кластер Windows, настройте среду разработки Windows. [Установите Visual Studio 2017](http://www.visualstudio.com), а также рабочие нагрузки **разработка Azure**, **ASP.NET и веб-разработка** и **кроссплатформенная разработка .NET Core**.  Теперь настройте [среду разработки .NET](service-fabric-get-started.md).
- Если вы развертываете кластер Linux, настройте среду разработки Java в [Linux](service-fabric-get-started-linux.md) или [MacOS](service-fabric-get-started-mac.md).  Установите [интерфейс командной строки Service Fabric](service-fabric-cli.md). 

## <a name="network-topology"></a>Топология сети
Теперь, когда в Azure имеется защищенный [кластер Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) или [кластер Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md), можно развернуть службу управления API в подсети виртуальной сети и NSG, предназначенных для нее. В рамках этого руководства шаблон Resource Manager для службы управления API предварительно настроен для использования имен виртуальной сети, подсети и группы безопасности сети, настроенных в предыдущем руководстве по кластеру [Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) или [Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md). В этом руководстве в Azure развертывается представленная ниже топология, в которой служба управления API и Service Fabric находятся в разных подсетях одной виртуальной сети:

 ![Рисунок][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Вход в Azure и выбор подписки
Войдите в учетную запись Azure и выберите подписку, прежде чем выполнять команды Azure.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Развертывание внутренней службы Service Fabric

Прежде чем настроить в управлении API маршрутизацию трафика к серверной службе Service Fabric, необходимо создать и запустить эту службу для приема запросов.  Если вы ранее создали [кластер Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md), разверните службу .NET Service Fabric.  Если вы ранее создали [кластер Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md), разверните службу Java Service Fabric.

### <a name="deploy-a-net-service-fabric-service"></a>Развертывание службы .NET Service Fabric

Для целей этого руководства нужна базовая надежная служба ASP.NET Core без отслеживания состояния, которую вы можете создать с помощью стандартного шаблона проекта веб-API. Так мы получим конечную точку HTTP для службы, которую вы позднее предоставите через службу управления API Azure.

Запустите Visual Studio от имени администратора и создайте службу ASP.NET Core:

 1. В Visual Studio выберите последовательно «Файл» -> «Создать проект».
 2. Выберите шаблон приложения Service Fabric в облаке и присвойте ему имя **ApiApplication**.
 3. Выберите шаблон службы ASP.NET Core без отслеживания состояния и присвойте проекту имя **WebApiService**.
 4. Выберите шаблон проекта ASP.NET Core 2.0 для веб-API.
 5. После создания проекта откройте файл `PackageRoot\ServiceManifest.xml` и удалите атрибут `Port` из конфигурации ресурса конечной точки:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    После удаления порт в Service Fabric будет динамически выбираться из диапазона портов приложения, который мы открыли через группу безопасности сети в шаблоне Resource Manager для кластера. К нему будет направляться трафик из службы управления API.
 
 6. Нажмите клавишу F5 в Visual Studio, чтобы убедиться, что веб-API доступен локально. 

    Откройте Service Fabric Explorer и просмотрите подробные сведения об определенном экземпляре службы ASP.NET Core, чтобы увидеть базовый адрес, который прослушивает служба. Добавьте `/api/values` к базовому адресу и откройте его в браузере, чтобы вызвать метод Get из ValuesController в шаблоне веб-API. Он вернет ответ по умолчанию, предоставленный шаблоном, — массив JSON, который содержит две строки:

    ```json
    ["value1", "value2"]`
    ```

    Эту конечную точку вы предоставите через службу управления API в Azure.

 7. Теперь вы можете развернуть приложение в кластере в Azure. В Visual Studio щелкните правой кнопкой мыши проект приложения и выберите **Опубликовать**. Укажите конечную точку кластера (например, `mycluster.southcentralus.cloudapp.azure.com:19000`) для развертывания приложения в кластер Service Fabric в Azure.

В кластере Service Fabric в Azure должна запуститься служба ASP.NET Core без отслеживания состояния с именем `fabric:/ApiApplication/WebApiService`.

### <a name="create-a-java-service-fabric-service"></a>Создание службы Java Service Fabric
Для этого руководства разверните базовый веб-сервер, который возвращает пользователю сообщения. Приложение сервера, возвращающего сообщения, содержит конечную точку HTTP для службы, которую вы позднее предоставите через службу управления API Azure.

1. Клонируйте примеры Java для начала работы.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started/reliable-services-actor-sample
   ```

2. Измените файл *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml*. Измените параметры конечной точки, чтобы служба прослушивала порт 8081.

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. Сохраните *ServiceManifest.xml*, затем выполните сборку приложения EchoServer1.0.

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. Разверните приложение в кластере.

   ```bash
   cd Scripts
   sfctl cluster select --endpoint https://mycluster.southcentralus.cloudapp.azure.com:19080 --pem <full_path_to_pem_on_dev_machine> --no-verify
   ./install.sh
   ```

   В кластере Service Fabric в Azure должна запуститься служба Java без отслеживания состояния с именем `fabric:/EchoServerApplication/EchoServerService`.

5. Откройте браузер и введите http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage. Должен отобразиться текст "[версия 1.0] Hello World!!!". .

## <a name="download-and-understand-the-resource-manager-templates"></a>Загрузка и изучение шаблонов Resource Manager
Загрузите и сохраните следующие шаблоны Resource Manager и файл параметров:
 
- [network-apim.json][network-arm]
- [network-apim.parameters.json][network-parameters-arm]
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

Шаблон *network-apim.json* развертывает новую подсеть и группу безопасности сети в виртуальной сети, где развертывается кластер Service Fabric.

В следующих разделах описаны ресурсы, которые определяются в шаблоне *apim.json*. Дополнительные сведения вы можете получить по ссылкам на справочную документацию по шаблонам, которые представлены в каждом разделе. Параметры, определяемые в файле параметров *apim.parameters.json*, описаны ниже в этой статье.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
В [Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) описан экземпляр службы управления API: имя, номер SKU или уровень, расположение группы ресурсов, сведения об издателе, виртуальная сеть.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates
При помощи [Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) настраивается защита для управления API. Управление API должно пройти аутентификацию с помощью кластера Service Fabric для обнаружения службы с помощью сертификата клиента, у которого есть доступ к кластеру. В этом руководстве используется сертификат, указанный при создании кластера [Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) или [Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), который по умолчанию предоставляет доступ к кластеру. 

В этом руководстве используется один сертификат для аутентификации клиента и безопасности обмена данными между узлами кластера. Вы можете использовать отдельный сертификат клиента, если у вас есть сертификат, настроенный для доступа к кластеру Service Fabric. Укажите **имя**, **пароль** и **строку данных** (в формате base-64) закрытого ключа (PFX-файл) сертификата кластера, который вы указали при создании кластера Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends
В [Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) описана серверную службу, к которой перенаправляется трафик. 

Для серверных систем Service Fabric роль серверной службы выполняет сам кластер Service Fabric, а не определенная служба Service Fabric. Это позволяет направлять политике запросы не в одну службу в кластере, а в несколько. Поле **url** здесь представляет полное доменное имя службы в кластере. По нему по умолчанию направляются все запросы, для которых во внутренней политике не указано имя службы. Вы можете использовать фиктивное имя службы, например "fabric:/fake/service", если вам не нужна резервная служба. **resourceId** определяет конечную точку управления кластером.  **clientCertificateThumbprint** и **serverCertificateThumbprints** определяют сертификаты, которые используются для аутентификации в кластере.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products
При помощи [Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) создается продукт. В службе управления API Azure продукт содержит один или несколько API вместе с квотой и условиями использования. Как только продукт будет опубликован, разработчики могут подписаться на него и начать использовать его интерфейсы API. 

Введите для продукта описательное имя **displayName** и описание **description**. Для целей этого руководства вам нужна подписка, но ее не обязательно подтверждать у администратора.  Для этого продукта **состояние** имеет значение "опубликовано". Это значит, что он доступен для подписчиков. 

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis
При помощи [Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) создается API-интерфейс. API в API Management представляет набор операций, которые могут быть вызваны клиентскими приложениями. После добавления операций интерфейс API добавляется к продукту и может быть опубликован. После публикации API на него могут подписаться разработчики для дальнейшего использования.

- Для параметра **displayName** можно указать любое имя API-интерфейса. В этом руководстве введите имя Service Fabric App.
- Параметр **name** содержит уникальное описательное имя для API-интерфейса, например service-fabric-app. Оно выводится на порталах разработчика и издателя. 
- Параметр **serviceUrl** определяет HTTP-службу, которая реализует API-интерфейс. Портал управления API направит запросы по этому адресу. Для серверных систем Service Fabric это значение URL-адреса не используется. Здесь вы можете использовать любое значение. Для выполнения задач этого руководства используйте, например, http://servicefabric. 
- Значение **path** добавляется к основному URL-адресу вашей службы управления API. Основной URL-адрес является общим для всех интерфейсов API, размещенных в экземпляре службы API Management. API Management отличает интерфейсы API по их суффиксу. Следовательно, суффикс должен быть уникальным для каждого API для заданного издателя. 
- Параметр **protocols** определяет, какие протоколы можно использовать для доступа к API. Для этого руководства укажите протоколы **http** и **https**.
- Параметр **path** содержит суффикс для API-интерфейса. Мы будем использовать суффикс myapp.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations
[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) содержит операции, которые нужно добавить в API-интерфейс, прежде чем служба управления API сможет его использовать.  Внешние клиенты могут использовать операции для взаимодействия со службой ASP.NET Core без отслеживания состояния, которая выполняется в кластере Service Fabric.

Чтобы добавить операцию к внешнему API-интерфейсу, укажите следующие значения:

- **displayName** (отображаемое имя) и **description** (описание) для идентификации операции. В этом руководстве введите Values.
- **method** (метод) — определяет команду HTTP.  В этом примере укажите **GET**.
- Шаблон URL-адреса **urlTemplate**, добавляемый к базовому URL-адресу API-интерфейса, идентифицирует конкретную HTTP-операцию.  В этом примере укажите `/api/values`, если вы добавили серверную службу .NET, или `getMessage`, если вы добавили серверную службу Java.  По умолчанию указанный здесь URL-адрес представляет собой URL-адрес, отправленный во внутреннюю службу Service Fabric. Если здесь вы используете тот же URL-адрес, который использует служба (/api/values), операция будет выполняться без дополнительных настроек. Вы также можете указать здесь URL-адрес, отличный от URL-адреса, используемого внутренней службой Service Fabric. В этом случае следует настроить перезапись пути в политике работы.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies
При помощи [Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) создается политика серверной части, которая объединяет все данные. Она требуется при настройке внутренней службы Service Fabric, в которую направляются запросы. Вы можете применить эту политику к любой API-операции.  Дополнительные сведения см. в [обзоре политик](/azure/api-management/api-management-howto-policies). 

[Конфигурация серверной части для Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) предоставляет следующие элементы управления маршрутизации запросов: 
 - Выбор экземпляра службы с помощью указания имени экземпляра службы Service Fabric. Оно может быть жестко запрограммированным (например, `"fabric:/myapp/myservice"`) или созданным с помощью HTTP-запроса (например, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Разрешение раздела путем создания ключа секции с помощью любой схемы секционирования Service Fabric.
 - Выбор реплики для служб с отслеживанием состояния.
 - Условия, при которых разрешение выполняется повторно, что позволяет указать условия для повторного разрешения расположения службы и повторной отправки запроса.

**policyContent** содержит политику в JSON-сериализованном формате XML.  В рамках этого руководства создайте внутреннюю политику, направляющую запросы напрямую в службу без отслеживания состояния .NET или Java, которую вы развернули ранее. Добавьте политику `set-backend-service` в узел входящих политик.  Замените значение *sf-service-instance-name* значением `fabric:/ApiApplication/WebApiService`, если вы развернули серверную службу .NET, или значением `fabric:/EchoServerApplication/EchoServerService`, если вы используете службу Java.  *backend-id* ссылается на внутренний ресурс, в нашем примере это ресурс `Microsoft.ApiManagement/service/backends`, определенный в шаблоне *apim.json*. *backend-id* может также ссылаться на другой внутренний ресурс, созданный с помощью API-интерфейсов управления API. В этом руководстве для *backend-id* присваивается значение параметра *service_fabric_backend_name*.
    
```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Чтобы ознакомиться с полным набором атрибутов серверной политики Service Fabric, см. дополнительные сведения в разделе [Задание внутренней службы](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService).

## <a name="set-parameters-and-deploy-api-management"></a>Настройка параметров и развертывание управления API
Для описанных ниже параметров без значений в файле *cluster.parameters.json* укажите сведения о своем развертывании. 

|Параметр|Значение|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Developer|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6| 
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;строка в формате base-64&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;строка XML&gt;|

Значения *certificatePassword* и *serviceFabricCertificateThumbprint* должны соответствовать сертификату кластера, который вы использовали для настройки кластера.  

*serviceFabricCertificate* содержит сертификат в формате строки base-64, которую можно создать с помощью следующего скрипта:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

В узле *inbound_policy* замените значение *sf-service-instance-name* значением `fabric:/ApiApplication/WebApiService`, если вы развернули серверную службу .NET, или значением `fabric:/EchoServerApplication/EchoServerService`, если вы используете службу Java. *backend-id* ссылается на внутренний ресурс, в нашем примере это ресурс `Microsoft.ApiManagement/service/backends`, определенный в шаблоне *apim.json*. *backend-id* может также ссылаться на другой внутренний ресурс, созданный с помощью API-интерфейсов управления API. В этом руководстве для *backend-id* присваивается значение параметра *service_fabric_backend_name*.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Используйте приведенный ниже сценарий, чтобы развернуть шаблон Resource Manager и файлы параметров для службы управления API.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="test-it"></a>Тестирование

Теперь попробуйте отправить запрос к внутренней службе Service Fabric через службу управления API непосредственно с [портала Azure](https://portal.azure.com).

 1. В службе управления API выберите **API**.
 2. В API **Service Fabric App**, созданном на предыдущих шагах, перейдите на вкладку **Тест** и выберите операцию **Values**.
 3. Нажмите кнопку **Отправка**, чтобы отправить тестовый запрос во внутреннюю службу.  Ответ HTTP будет выглядеть следующим образом:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT

    
    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Помимо собственных ресурсов кластер содержит другие ресурсы Azure. Чтобы удалить кластер и все ресурсы, который он использует, проще всего удалить группу ресурсов.

Войдите в Azure и выберите идентификатор подписки, в которой вы хотите удалить кластер.  Идентификатор подписки можно узнать, войдя на [портал Azure](http://portal.azure.com). Удалите группу ресурсов и все ресурсы кластера с помощью командлета [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Развертывание управления API
> * Настройка управления API
> * Создание операции API
> * Настройка внутренней политики
> * Добавление API в продукт

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
