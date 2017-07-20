---
title: "Краткое руководство по управлению API и Azure Service Fabric | Документация Майкрософт"
description: "Это руководство поможет быстро начать работу со службой управления API Azure и Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 2160e2e65de5c65df8a13248bad4f626def86e49
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017


---

# <a name="service-fabric-with-azure-api-management-quick-start"></a>Краткое руководство по Service Fabric со службой управления API Azure

С помощью этого руководства вы узнаете, как настроить службу управления API Azure с Service Fabric, а также как настроить первую операцию API для отправки трафика к службам серверной части в Service Fabric. Дополнительные сведения о сценариях службы управления API Azure и Service Fabric см. в [обзорной статье](service-fabric-api-management-overview.md). 

## <a name="deploy-api-management-and-service-fabric-to-azure"></a>Развертывание службы управления API и Service Fabric в Azure

Сначала необходимо развернуть службу управления API и кластер Service Fabric в Azure в общедоступной виртуальной сети. Это позволит службе управления API напрямую взаимодействовать с Service Fabric, что обеспечит обнаружение службы, разрешение раздела службы, а также перенаправление трафика прямо в любую внутреннюю службу в Service Fabric.

### <a name="topology"></a>Топология

В этом руководстве развертывается следующая топология в Azure, в которой служба управления API и Service Fabric находятся в подсетях одной виртуальной сети:

 ![Рисунок][sf-apim-topology-overview]

Чтобы быстро приступить к работе, для каждого шага развертывания предоставляются шаблоны Resource Manager.

 - Топология сети:
    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]
 - Кластер Service Fabric:
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]
 - Управление API
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

### <a name="sign-in-to-azure-and-select-your-subscription"></a>Вход в Azure и выбор подписки

В этом руководстве используется [Azure PowerShell][azure-powershell]. При запуске нового сеанса PowerShell войдите в свою учетную запись Azure и выберите подписку перед выполнением команд Azure.
 
Вход в учетную запись Azure:

```powershell
PS > Login-AzureRmAccount
```

Выберите свою подписку.

```powershell
PS > Get-AzureRmSubscription
PS > Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте новую группу ресурсов для развертывания. Назначьте ей имя и расположение.

```powershell
PS > New-AzureRmResourceGroup -Name <my-resource-group> -Location westus
```

### <a name="deploy-the-network-topology"></a>Развертывание топологии сети

Сначала необходимо настроить топологию сети, в которой будут развернуты служба управления API и кластер Service Fabric. Шаблон Resource Manager [network.json][network-arm] настроен на создание виртуальной сети (VNET) с двумя подсетями и двумя группами безопасности сети (NSG). 

Файл параметров [network.parameters.json][network-parameters-arm] содержит имена подсетей и групп безопасности сети, в которых будут развернуты служба управления API и Service Fabric. В рамках этого руководства изменять значения параметров не нужно. Шаблоны Resource Manager управления API и Service Fabric используют эти значения, поэтому если их изменить, вам придется их также изменить в других шаблонах Resource Manager. 

 1. Скачайте шаблон Resource Manager и файл параметров:

    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]

 2. Используйте следующую команду PowerShell для развертывания шаблона Resource Manager и файла параметров для настройки сети:

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
    ```

### <a name="deploy-the-service-fabric-cluster"></a>Развертывание кластера Service Fabric

Когда сетевые ресурсы развернуты, необходимо развернуть кластер Service Fabric в виртуальной сети в подсети и группе безопасности сети, используемых для кластера Service Fabric. В рамках этого руководства шаблон Resource Manager Service Fabric предварительно настроен на использование имен виртуальной сети, подсети и группы безопасности сети, настроенных на предыдущем шаге. 

Шаблон Resource Manager кластера Service Fabric настроен для создания безопасного кластера с сертификатом безопасности. Сертификат используется, чтобы обеспечить для кластера безопасный обмен данными между узлами, а также чтобы управлять доступом пользователя к кластеру Service Fabric. Управление API использует этот сертификат, чтобы получить доступ к службе именования Service Fabric для ее обнаружения.

Этот шаг требует наличия сертификата в Key Vault для обеспечения безопасности кластера. Дополнительные сведения о настройке безопасного кластера с помощью Key Vault см. в статье [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

> [!NOTE]
> Вы можете добавить аутентификацию Azure Active Directory (кроме сертификата, используемого для доступа к кластеру). Azure Active Directory — рекомендуемый, но не обязательный для этого руководства способ управления доступом пользователя к кластеру Service Fabric. Сертификат является обязательным для обеспечения безопасности кластера при обмене данными между узлами, а также для аутентификации службы управления API Azure, которая в настоящее время не поддерживает аутентификацию с помощью Azure Active Directory для серверной части Service Fabric.

 1. Скачайте шаблон Resource Manager и файл параметров:
 
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]

 2. Заполните пустые параметры в файле `cluster.parameters.json` для развертывания, включая [сведения о Key Vault](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault) для сертификата кластера.

 3. Используйте следующую команду PowerShell для развертывания шаблона Resource Manager и файла параметров для создания кластера Service Fabric:

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
    ```

### <a name="deploy-api-management"></a>Развертывание управления API

Наконец можно развернуть управление API в виртуальной сети в подсети и группе безопасности сети, настроенных для управления API. Для развертывания управления API вам не нужно ждать, пока завершится развертывание кластера Service Fabric. 

В рамках этого руководства шаблон Resource Manager управления API предварительно настроен на использование имен виртуальной сети, подсети и группы безопасности сети, настроенных на предыдущем шаге. 

 1. Скачайте шаблон Resource Manager и файл параметров:
 
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

 2. Заполните пустые параметры в файле `apim.parameters.json` для развертывания.

 3. Используйте следующую команду PowerShell для развертывания шаблона Resource Manager и файла параметров для управления API:

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
    ```

## <a name="configure-api-management"></a>Настройка управления API

После развертывания кластера Service Fabric и управления API можно настроить серверную часть Service Fabric в службе управления API. Это позволит создать политику внутренней службы, которая отправляет трафик в кластер Service Fabric.

### <a name="api-management-security"></a>Безопасность управления API

Чтобы настроить серверную часть Service Fabric, сначала необходимо настроить параметры безопасности управления API. Чтобы настроить параметры безопасности, перейдите в службу управления API на портале Azure.

#### <a name="enable-the-api-management-rest-api"></a>Включение REST API службы управления API

В настоящее время REST API службы управления API является единственным способом настроить внутреннюю службу. Сначала нужно включить REST API службы управления API и обеспечить ее безопасность.

 1. В службе управления API выберите **Management API - PREVIEW** (API управления (предварительная версия)) в разделе **Безопасность**.
 2. Установите флажок для параметра **включения REST API службы управления API**.
 3. Запишите URL-адрес API управления. Мы используем его позже для настройки внутренней службы Service Fabric.
 4. Создайте **маркер доступа**, выбрав дату окончания действия и ключ, а затем нажмите кнопку **Создать** в нижней области страницы.
 5. Скопируйте **маркер доступа** и сохраните его (он нам понадобится дальше). Обратите внимание, что он отличается от первичного и вторичного ключа.

#### <a name="upload-a-service-fabric-client-certificate"></a>Отправка клиентского сертификата Service Fabric

Управление API должно пройти аутентификацию с помощью кластера Service Fabric для обнаружения службы с помощью сертификата клиента, у которого есть доступ к кластеру. Для удобства в этом руководстве используется тот же сертификат, указанный при создании кластера Service Fabric, который по умолчанию может использоваться для доступа к кластеру.

 1. В службе управления API выберите **Client certificates - PREVIEW** (Сертификаты клиента (предварительная версия)) в разделе **Безопасность**.
 2. Нажмите кнопку **Добавить**.
 2. Выберите файл закрытого ключа (PFX-файл) сертификата кластера, указанного при создании кластера Service Fabric, присвойте ему имя и укажите пароль для закрытого ключа.

> [!NOTE]
> В этом руководстве используется один сертификат для аутентификации клиента и безопасности обмена данными между узлами кластера. Вы можете использовать отдельный сертификат клиента, если у вас есть сертификат, настроенный для доступа к кластеру Service Fabric.

### <a name="configure-the-backend"></a>Настройка внутренней службы

Теперь, когда безопасность управления API настроена, можно настроить внутреннюю службу Service Fabric. Для серверных систем кластер Service Fabric представляет собой внутреннюю службу, а не определенную службу Service Fabric. Это позволяет направлять политике запросы не в одну службу в кластере, а в несколько.

Этот шаг требует созданный ранее маркер доступа, а также отпечаток сертификата кластера, загруженный в службу управления API на предыдущем шаге.

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

Параметр **URL-адреса** здесь представляет полное доменное имя службы в кластере, куда все запросы направляются по умолчанию, если во внутренней политике не указано имя службы. Вы можете использовать фиктивное имя службы, например "fabric:/fake/service", если вам не нужна резервная служба.

Дополнительные сведения о каждом свойстве серверной части см. в [справочной документации по API для серверной части](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend).

#### <a name="example"></a>Пример

```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
Authorization: SharedAccessSignature 230948023984&Ld93cRGcNU6KZ4uVz7JlfTec4eX43Q9Nu8ndatOgBzs6+f559Pkf3iHX2cSge+r42pn35qGY3TitjrIl13hwcQ==
Content-Type: application/json

{
    "description": "My Service Fabric backend",
    "url": "fabric:/myapp/myservice",
    "protocol": "http",
    "resourceId": "https://your-cluster.westus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": ["https://your-cluster.westus.cloudapp.azure.com:19080"],
            "clientCertificateThumbprint": "57bc463aba3aea3a12a18f36f44154f819f0fe32",
            "serverCertificateThumbprints": ["57bc463aba3aea3a12a18f36f44154f819f0fe32"],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Развертывание внутренней службы Service Fabric

Теперь, когда Service Fabric настроена в качестве серверной части управления API, можно создать внутренние политики для API-интерфейсов, отправляющие трафик в службы Service Fabric. Но сначала вам потребуется служба, работающая в Service Fabric, чтобы принимать запросы.

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>Создание службы Service Fabric с конечной точкой HTTP

В рамках этого руководства мы создадим базовую надежную службу ASP.NET Core без отслеживания состояния с помощью шаблона проекта веб-API по умолчанию. Так мы получим конечную точку HTTP для службы, которую можно будет использовать через службу управления API Azure:

```
/api/values
```

Ознакомьтесь с разделом о [настройке среды разработки для разработки ASP.NET Core](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core).

После настройки среды разработки запустите Visual Studio от имени администратора и создайте службу ASP.NET Core:

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

    Это конечная точка, которую вы предоставите через службу управления API в Azure.

 7. Теперь вы можете развернуть приложение в кластере в Azure. **В Visual Studio** щелкните правой кнопкой мыши проект приложения и выберите [Опубликовать](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box). Укажите конечную точку кластера (например, `mycluster.westus.cloudapp.azure.com:19000`) для развертывания приложения в кластер Service Fabric в Azure.

В кластере Service Fabric в Azure должна запуститься служба ASP.NET Core без отслеживания состояния с именем `fabric:/ApiApplication/WebApiService`.

## <a name="create-an-api-operation"></a>Создание операции API

Теперь все готово для создания операции в службе управления API, которую внешние клиенты могут использовать для взаимодействия со службой ASP.NET Core без отслеживания состояния, выполняющейся в кластере Service Fabric.

 1. Войдите на портал Azure и перейдите к развертыванию службы управления API.
 2. В колонке службы управления API выберите **API-интерфейсы (ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ)**.
 3. Добавьте новый API, щелкнув поле **Blank API** (Пустой API) и заполнив следующие параметры диалогового окна:

     - **URL-адрес веб-службы**: для серверных систем Service Fabric это значение URL-адреса не используется. Здесь вы можете использовать любое значение. В рамках этого руководства используйте `http://servicefabric`.
     - **Имя**: укажите любое имя для API. В рамках этого руководства используйте `Service Fabric App`.
     - **Схема URL-адреса**: выберите HTTP, HTTPS или оба варианта. В рамках этого руководства используйте `both`.
     - **API URL Suffix** (Суффикс URL-адреса API): укажите суффикс для API. В рамках этого руководства используйте `myapp`.
 
 4. После создания API щелкните **Добавить операцию**, чтобы добавить интерфейсную операцию API. Заполните следующие значения:
    
     - **URL-адрес**: выберите `GET` и укажите URL-адрес для API. В рамках этого руководства используйте `/api/values`.
     
       По умолчанию указанный здесь URL-адрес представляет собой URL-адрес, отправленный во внутреннюю службу Service Fabric. Если вы здесь используете тот же URL-адрес, что и служба (в этом случае `/api/values`), тогда операция будет работать без изменений. Вы также можете указать здесь URL-адрес, отличный от URL-адреса, используемого внутренней службой Service Fabric. В этом случае вам позже нужно будет указать перезапись пути в политике операции.
     - **Отображаемое имя**: укажите любое имя для API. В рамках этого руководства используйте `Values`.

## <a name="configure-a-backend-policy"></a>Настройка внутренней политики

Внутренняя политика необходима для связи. Она требуется при настройке внутренней службы Service Fabric, в которую направляются запросы. Вы можете применить эту политику к любой API-операции. [Конфигурация серверной части для Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) предоставляет следующие элементы управления маршрутизации запросов: 
 - Выбор экземпляра службы с помощью указания имени экземпляра службы Service Fabric. Оно может быть жестко запрограммированным (например, `"fabric:/myapp/myservice"`) или созданным с помощью HTTP-запроса (например, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Разрешение раздела путем создания ключа секции с помощью любой схемы секционирования Service Fabric.
 - Выбор реплики для служб с отслеживанием состояния.
 - Условия, при которых разрешение выполняется повторно, что позволяет указать условия для повторного разрешения расположения службы и повторной отправки запроса.

В рамках этого руководства создайте внутреннюю политику, направляющую запросы напрямую в ранее развернутую службу ASP.NET Core без отслеживания состояния.

 1. Выберите и измените **входящие политики** для операции `Values`. Для этого щелкните значок редактирования, а затем выберите **Просмотр кода**.
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

 1. В службе управления API выберите **Products - PREVIEW** (Продукты (предварительная версия)).
 2. По умолчанию служба управления API предоставляет два продукта: фиксированный и без ограничений. Выберите продукт без ограничений.
 3. Выберите API-интерфейсы.
 4. Щелкните **+ Добавить**.
 5. Выберите созданный на предыдущем шаге API `Service Fabric App`, а затем нажмите кнопку **Выбрать**.

### <a name="test-it"></a>Тестирование

Теперь вы можете попробовать отправить запрос к внутренней службе Service Fabric через службу управления API непосредственно из портала Azure.

 1. В службе управления API выберите **API-интерфейсы (ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ)**.
 2. В созданном на предыдущем шаге API `Service Fabric App` выберите вкладку **Проверить**.
 3. Нажмите кнопку **Отправка**, чтобы отправить тестовый запрос во внутреннюю службу.

## <a name="next-steps"></a>Дальнейшие действия

На этом этапе базовая установка службы управления API и Service Fabric завершена.

Для быстрой настройки в рамках этого руководства используется базовая аутентификация пользователя на основе сертификата для кластера Service Fabric. Расширенную аутентификацию пользователя для кластера Service Fabric лучше использовать с [аутентификацией Azure Active Directory](service-fabric-cluster-creation-via-arm.md#set-up-azure-active-directory-for-client-authentication). 

Далее, чтобы подготовить приложение для реального трафика, [создайте и настройте расширенные параметры продукта в службе управления API Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-product-with-rules).

<!-- links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json


<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-api-management-quickstart/sf-apim-topology-overview.png

