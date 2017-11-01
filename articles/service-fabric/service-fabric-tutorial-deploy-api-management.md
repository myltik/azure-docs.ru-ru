---
title: "Интеграция Azure Service Fabric со службой управления API | Документация Майкрософт"
description: "Узнайте, как быстро приступить к работе со службой управления API Azure и Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/13/2017
ms.author: ryanwi
ms.openlocfilehash: d98d2823c19f24a2d9040f7959bd5189bd6bcc16
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Развертывание службы управления API с помощью Service Fabric
Это руководство представляет собой вторую часть цикла. В нем показано, как настроить [службу управления API Azure](../api-management/api-management-key-concepts.md) с помощью Service Fabric для перенаправления трафика во внутреннюю службу в Service Fabric.  Выполнив инструкции из этого руководства, вы развернете службу управления API в виртуальной сети и настроите API для отправки трафика во внутренние службы без отслеживания состояния. Дополнительные сведения о сценариях службы управления API Azure и Service Fabric см. в [обзорной статье](service-fabric-api-management-overview.md).

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
> * развертывание службы управления API с помощью Service Fabric.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [модуль Azure PowerShell версии 4.1 или более поздней версии](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) либо [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Создайте защищенный [кластер Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) или [кластер Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) в Azure.
- Если вы развертываете кластер Windows, настройте среду разработки Windows. [Установите Visual Studio 2017](http://www.visualstudio.com), а также рабочие нагрузки **разработка Azure**, **ASP.NET и веб-разработка** и **кроссплатформенная разработка .NET Core**.  Теперь настройте [среду разработки .NET](service-fabric-get-started.md).
- Если вы развертываете кластер Linux, настройте среду разработки Java в [Linux](service-fabric-get-started-linux.md) или [MacOS](service-fabric-get-started-mac.md).  Установите [интерфейс командной строки Service Fabric](service-fabric-cli.md). 

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Вход в Azure и выбор подписки
Войдите в учетную запись Azure и выберите подписку, прежде чем выполнять команды Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-api-management"></a>Развертывание управления API
Обычно, облачным приложениям требуется интерфейсный шлюз, который предоставляет единую точку передачи входящего трафика пользователей, устройств или других приложений. В Service Fabric в качестве шлюза может выступать любая служба без отслеживания состояния, например приложение ASP.NET Core, или другая служба, предназначенная для обработки входящего трафика, например концентраторы событий, Центр Интернета вещей или служба управления API Azure. Это руководство содержит вводную информацию об использовании службы управления API Azure в качестве шлюза для приложений Service Fabric. Служба управления API непосредственно интегрируется с Service Fabric. Это позволяет публиковать интерфейсы API с широким набором правил маршрутизации к внутренним службам Service Fabric. 

Теперь, когда в Azure имеется защищенный [кластер Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) или [кластер Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md), можно развернуть службу управления API в подсети виртуальной сети и NSG, предназначенных для нее. В рамках этого руководства шаблон Resource Manager для службы управления API предварительно настроен для использования имен виртуальной сети, подсети и группы безопасности сети, настроенных в предыдущем руководстве по кластеру [Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) или [Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md). 

Скачайте шаблон Resource Manager и файл параметров:
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

Заполните пустые параметры в файле `apim.parameters.json` для развертывания.

Используйте приведенный ниже сценарий, чтобы развернуть шаблон Resource Manager и файлы параметров для службы управления API.

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>Настройка управления API

После развертывания кластера Service Fabric и службы управления API можно настроить параметры безопасности и серверную часть Service Fabric в службе управления API. Это позволит создать политику внутренней службы, которая отправляет трафик в кластер Service Fabric.

### <a name="configure-api-management-security"></a>Настройка безопасности службы управления API

Чтобы настроить серверную часть Service Fabric, сначала необходимо настроить параметры безопасности управления API. Чтобы настроить параметры безопасности, перейдите в службу управления API на портале Azure.

#### <a name="enable-the-api-management-rest-api"></a>Включение REST API службы управления API

В настоящее время REST API службы управления API является единственным способом настроить внутреннюю службу. Сначала нужно включить REST API службы управления API и обеспечить ее безопасность.

 1. В службе управления API в разделе **Безопасность** выберите **API управления**.
 2. Установите флажок для параметра **включения REST API службы управления API**.
 3. Запишите **URL-адрес API управления**. Мы используем его позже для настройки серверной части Service Fabric.
 4. Создайте **маркер доступа**, выбрав дату окончания действия и ключ, а затем нажмите кнопку **Создать** внизу страницы.
 5. Скопируйте **маркер доступа** и сохраните его.  Этот маркер доступа понадобится нам на следующих шагах. Обратите внимание, что он отличается от первичного и вторичного ключа.

#### <a name="upload-a-service-fabric-client-certificate"></a>Отправка клиентского сертификата Service Fabric

Управление API должно пройти аутентификацию с помощью кластера Service Fabric для обнаружения службы с помощью сертификата клиента, у которого есть доступ к кластеру. Для удобства в этом руководстве используется сертификат, указанный при создании кластера [Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) или [Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), который по умолчанию может использоваться для доступа к кластеру.

 1. В службе управления API в разделе **Безопасность** выберите **Сертификаты клиентов**.
 2. Щелкните **+ Добавить**.
 2. Выберите файл закрытого ключа (PFX-файл) сертификата кластера, указанного при создании кластера Service Fabric, присвойте ему имя и укажите пароль для закрытого ключа.

> [!NOTE]
> В этом руководстве используется один сертификат для аутентификации клиента и безопасности обмена данными между узлами кластера. Вы можете использовать отдельный сертификат клиента, если у вас есть сертификат, настроенный для доступа к кластеру Service Fabric.

### <a name="configure-the-backend"></a>Настройка внутренней службы

Теперь, когда безопасность управления API настроена, можно настроить внутреннюю службу Service Fabric. Для серверных систем кластер Service Fabric представляет собой внутреннюю службу, а не определенную службу Service Fabric. Это позволяет направлять политике запросы не в одну службу в кластере, а в несколько.

На этом шаге требуется созданный ранее маркер доступа и отпечаток сертификата кластера, переданный в службу управления API на предыдущем шаге.

> [!NOTE]
> Если вы использовали отдельный клиентский сертификат на предыдущем шаге для управления API, на этом шаге вам потребуется отпечаток сертификата клиента в дополнение к отпечатку сертификата кластера.

Отправьте следующий HTTP-запрос PUT на URL-адрес API службы управления API, записанный ранее при включении REST API для службы управления API, для настройки внутренней службы Service Fabric. При успешном выполнении команды вы увидите ответ `HTTP 201 Created`. Дополнительные сведения о каждом свойстве серверной части см. в [справочной документации по API для серверной части](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend).

Команда HTTP и URL-адрес:
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

Заголовки запроса:
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

Тело запроса:
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

Параметр **url** здесь представляет полное доменное имя службы в кластере, на которое по умолчанию направляются все запросы, для которых во внутренней политике не указано имя службы. Вы можете использовать фиктивное имя службы, например "fabric:/fake/service", если вам не нужна резервная служба.

Дополнительные сведения о каждом свойстве серверной части см. в [справочной документации по API для серверной части](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend).

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Развертывание внутренней службы Service Fabric

Теперь, когда Service Fabric настроена в качестве серверной части управления API, можно создать внутренние политики для API-интерфейсов, отправляющие трафик в службы Service Fabric. Но сначала вам потребуется служба, работающая в Service Fabric, чтобы принимать запросы.  Если вы ранее создали [кластер Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md), разверните службу .NET Service Fabric.  Если вы ранее создали [кластер Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md), разверните службу Java Service Fabric.

### <a name="deploy-a-net-service-fabric-service"></a>Развертывание службы .NET Service Fabric

В рамках этого руководства мы создаем базовую надежную службу ASP.NET Core без отслеживания состояния с помощью стандартного шаблона проекта веб-API. Так мы получим конечную точку HTTP для службы, которую вы позднее предоставите через службу управления API Azure.

```
/api/values
```

Запустите Visual Studio от имени администратора и создайте службу ASP.NET Core:

 1. В Visual Studio выберите последовательно «Файл» -> «Создать проект».
 2. Выберите шаблон приложения Service Fabric в облаке и присвойте ему имя **ApiApplication**.
 3. Выберите шаблон службы ASP.NET Core и назовите проект **WebApiService**.
 4. Выберите шаблон проекта ASP.NET Core 1.1 для веб-API.
 5. После создания проекта откройте файл `PackageRoot\ServiceManifest.xml` и удалите атрибут `Port` из конфигурации ресурса конечной точки:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Это позволит Service Fabric динамически указать порт из диапазона портов приложения, который мы открыли через группу безопасности сети в шаблоне Resource Manager кластера, позволяя направлять к нему трафик из службы управления API.
 
 6. Нажмите клавишу F5 в Visual Studio, чтобы убедиться, что веб-API доступен локально. 

    Откройте Service Fabric Explorer и просмотрите подробные сведения об определенном экземпляре службы ASP.NET Core, чтобы увидеть базовый адрес, который прослушивает служба. Добавьте `/api/values` в базовый адрес и откройте его в браузере. При этом будет вызван метод Get объекта ValuesController в шаблоне веб-API. Он вернет ответ по умолчанию, предоставленный шаблоном, — массив JSON, который содержит две строки:

    ```json
    ["value1", "value2"]`
    ```

    Эту конечную точку вы предоставите через службу управления API в Azure.

 7. Теперь вы можете развернуть приложение в кластере в Azure. **В Visual Studio** щелкните правой кнопкой мыши проект приложения и выберите [Опубликовать](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box). Укажите конечную точку кластера (например, `mycluster.southcentralus.cloudapp.azure.com:19000`) для развертывания приложения в кластер Service Fabric в Azure.

В кластере Service Fabric в Azure должна запуститься служба ASP.NET Core без отслеживания состояния с именем `fabric:/ApiApplication/WebApiService`.

### <a name="create-a-java-service-fabric-service"></a>Создание службы Java Service Fabric
В рамках этого руководства мы развертываем базовый веб-сервер, который возвращает пользователю сообщения. Приложение сервера, возвращающего сообщения, содержит конечную точку HTTP для службы, которую вы позднее предоставите через службу управления API Azure.

1. Клонируйте примеры Java для начала работы.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started
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
   sfctl cluster select --endpoint http://mycluster.southcentralus.cloudapp.azure.com:19080
   ./install.sh
   ```

   В кластере Service Fabric в Azure должна запуститься служба Java без отслеживания состояния с именем `fabric:/EchoServerApplication/EchoServerService`.

5. Откройте браузер и введите в адресной строке http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage. Вы увидите сообщение "[version 1.0] Hello World!!!" .


## <a name="create-an-api-operation"></a>Создание операции API

Теперь все готово для создания операции в службе управления API, которую внешние клиенты могут использовать для взаимодействия со службой ASP.NET Core без отслеживания состояния, выполняющейся в кластере Service Fabric.

1. Войдите на портал Azure и перейдите к развертыванию службы управления API.
2. В колонке службы управления API выберите **Интерфейсы API**.
3. Добавьте новый API, щелкнув **+API**, выбрав поле **Пустой API** и заполнив параметры в диалоговом окне.

    - **URL-адрес веб-службы**: для серверных систем Service Fabric это значение URL-адреса не используется. Здесь вы можете использовать любое значение. В этом руководстве используйте http://servicefabric.
    - **Отображаемое имя**: укажите любое имя для API. В этом руководстве введите имя Service Fabric App.
    - **Имя**: Введите service-fabric-app.
    - **Схема URL-адреса**: выберите **HTTP**, **HTTPS** или **оба варианта**. В рамках данного руководства выберите **оба варианта**.
    - **API URL Suffix** (Суффикс URL-адреса API): укажите суффикс для API. Мы будем использовать суффикс myapp.
 
4. Выберите **Service Fabric App** из списка интерфейсов API и щелкните **+ Add operation** (+ Добавить операцию), чтобы добавить операцию внешнего API. Заполните следующие значения:
    
    - **URL-адрес**: выберите **GET** и укажите URL-путь для API. В этом руководстве введите путь /api/values.  По умолчанию указанный здесь URL-адрес представляет собой URL-адрес, отправленный во внутреннюю службу Service Fabric. Если вы здесь используете тот же URL-адрес, что и служба (в этом случае /api/values), то операция будет работать без изменений. Вы также можете указать здесь URL-адрес, отличный от URL-адреса, используемого внутренней службой Service Fabric. В этом случае следует настроить перезапись пути в политике работы.
    - **Отображаемое имя**: укажите любое имя для API. В этом руководстве введите Values.

5. Щелкните **Сохранить**.

## <a name="configure-a-backend-policy"></a>Настройка внутренней политики

Внутренняя политика необходима для связи. Она требуется при настройке внутренней службы Service Fabric, в которую направляются запросы. Вы можете применить эту политику к любой API-операции. [Конфигурация серверной части для Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) предоставляет следующие элементы управления маршрутизации запросов: 
 - Выбор экземпляра службы с помощью указания имени экземпляра службы Service Fabric. Оно может быть жестко запрограммированным (например, `"fabric:/myapp/myservice"`) или созданным с помощью HTTP-запроса (например, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Разрешение раздела путем создания ключа секции с помощью любой схемы секционирования Service Fabric.
 - Выбор реплики для служб с отслеживанием состояния.
 - Условия, при которых разрешение выполняется повторно, что позволяет указать условия для повторного разрешения расположения службы и повторной отправки запроса.

В рамках этого руководства создайте внутреннюю политику, направляющую запросы напрямую в ранее развернутую службу ASP.NET Core без отслеживания состояния.

 1. Выберите и измените **входящие политики** для операции Values. Для этого щелкните значок редактирования, а затем выберите **Просмотр кода**.
 2. В редакторе кода политики добавьте политику `set-backend-service` в раздел входящих политик, как показано ниже, а затем нажмите кнопку **Сохранить**:
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
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

### <a name="add-the-api-to-a-product"></a>Добавление API в продукт 

Прежде чем вызывать API, его нужно добавить в продукт, где вы можете предоставить пользователям доступ. 

 1. В службе управления API выберите **Продукты**.
 2. По умолчанию служба управления API предоставляет два продукта: фиксированный и без ограничений. Выберите продукт без ограничений.
 3. Щелкните **+Add APIs** (+ Добавить интерфейсы API).
 4. Выберите созданный на предыдущем шаге API `Service Fabric App`, а затем нажмите кнопку **Выбрать**.

### <a name="test-it"></a>Тестирование

Теперь вы можете попробовать отправить запрос к внутренней службе Service Fabric через службу управления API непосредственно из портала Azure.

 1. В службе управления API выберите **API**.
 2. В API **Service Fabric App**, созданном на предыдущих шагах, перейдите на вкладку **Тест** и выберите операцию **Values**.
 3. Нажмите кнопку **Отправка**, чтобы отправить тестовый запрос во внутреннюю службу.  Ответ HTTP будет выглядеть следующим образом:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Помимо собственных ресурсов кластер содержит другие ресурсы Azure. Чтобы удалить кластер и все ресурсы, который он использует, проще всего удалить группу ресурсов.

Войдите в Azure и выберите идентификатор подписки, в которой вы хотите удалить кластер.  Идентификатор подписки можно узнать, войдя на [портал Azure](http://portal.azure.com). Удалите группу ресурсов и все ресурсы кластера с помощью командлета [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Развертывание управления API
> * Настройка управления API
> * Создание операции API
> * Настройка внутренней политики
> * Добавление API в продукт


[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json