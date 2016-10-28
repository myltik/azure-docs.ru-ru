<properties
   pageTitle="Обратный прокси-сервер Service Fabric | Microsoft Azure"
   description="Использование обратного прокси-сервера Service Fabric для взаимодействия с микрослужбами изнутри и извне кластера"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman,vturecek"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="vturecek"/>

# Обратный прокси-сервер Service Fabric

Этот обратный прокси-сервер встроен в Service Fabri. Он обеспечивает адресацию микрослужб в кластере Service Fabric, предоставляющем конечные точки HTTP.

## Модель взаимодействия с микрослужбами

Микрослужбы в Service Fabric обычно выполняются на подмножестве виртуальных машин в кластере и по различным причинам могут перемещаться с одной виртуальной машины на другую. Поэтому конечные точки для микрослужб могут динамически изменяться. Типичной схемой взаимодействия с микрослужбами является цикл разрешения, приведенный ниже.

1. Первоначальное разрешение расположения службы через службу именования.
2. Подключение к службе.
3. Определение причины ошибки подключения и при необходимости повторное разрешение расположения службы.

Этот процесс обычно включает в себя заключение в оболочку клиентских библиотек связи в цикле повтора, реализующих политики разрешения службы и повторных попыток. Чтобы больше узнать об этом, ознакомьтесь с [взаимодействием со службами](service-fabric-connect-and-communicate-with-services.md).

### Взаимодействие через обратный прокси-сервер Service Fabric
Обратный прокси-сервер Service Fabric запускается на всех узлах в кластере. Он выполняет весь процесс разрешения службы от имени клиента, а затем пересылает запрос клиента. Поэтому клиенты, работающие в кластере, могут использовать любые клиентские библиотеки связи HTTP для обмена данными с целевой службой через обратный прокси-сервер Service Fabric, который выполняется локально на том же узле.

![Взаимодействие изнутри][1]

## Обращение к микрослужбам извне кластера
Моделью внешнего взаимодействия по умолчанию для микрослужб является **участие**: к службе по умолчанию нельзя получить доступ напрямую из внешних клиентов. [Azure Load Balancer](../load-balancer/load-balancer-overview.md) является границей сети между микрослужбами и внешними клиентами, которая выполняет преобразование сетевых адресов и пересылает внешние запросы к внутренним конечным точкам **IP-адрес:порт**. Чтобы внешние клиенты могли напрямую обращаться к конечной точке, нужно сначала настроить Azure Load Balancer для пересылки трафика каждого порта, используемого службой в кластере. Кроме того, большинство микрослужб (в особенности микрослужбы с отслеживанием состояния) не работают на всех узлах кластера и могут перемещаться между узлами при отработке отказа. Поэтому в таких случаях Azure Load Balancer не может эффективно определить целевой узел реплик для пересылки трафика на него.

### Обращение к микрослужбам через обратный прокси-сервер Service Fabric извне кластера

Вместо отдельных портов службы в Azure Load Balancer можно просто настроить порт обратного прокси-сервера Service Fabric. Это позволит клиентам за пределами кластера обращаться к службам внутри него через обратный прокси-сервер без дополнительных настроек.

![Взаимодействие извне][0]

>[AZURE.WARNING] Настройка порта обратного прокси-сервера в балансировщике нагрузки обеспечит адресацию извне кластера всех микрослужб в этом кластере, которые предоставляют конечную точку HTTP.


## Формат универсального кода ресурса (URI) для адресации служб через обратный прокси-сервер

Обратный прокси-сервер использует определенный формат универсального кода ресурса (URI), чтобы определять, в какую секцию службы следует переслать входящий запрос.

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http(s)**. Обратный прокси-сервер можно настроить для приема трафика HTTP или HTTPS. В случае трафика HTTPS функцию моста SSL выполняет обратный прокси-сервер. Запросы, которые пересылаются обратным прокси-сервером к службам в кластере, передаются по протоколу HTTP.
 - **Полное доменное имя кластера| internal IP:** For external clients, the reverse proxy can be configured so that it is reachable through the cluster domain (e.g., mycluster.eastus.cloudapp.azure.com). By default the reverse proxy runs on every node, so for internal traffic it can be reached on localhost or on any internal node IP (e.g., 10.0.0.1).
 - **Port**. Порт, указанный для обратного прокси-сервера. Пример: 19008.
 - **ServiceInstanceName**. Полное имя развернутого экземпляра службы, к которому вы пытаетесь получить доступ, без схемы "fabric:/". Например, чтобы подключиться к службе *fabric:/myapp/myservice/*, используйте *myapp/myservice*.
 - **Suffix path**. Фактический URL-адрес службы, к которой вы подключаетесь, например *myapi/values/add/3*.
 - **PartitionKey**. Для секционированной службы это вычисляемый ключ секции, к которой вы подключаетесь. Обратите внимание, что это *не* идентификатор GUID секции. Этот параметр не является обязательным для служб, использующих схему одноэлементного секционирования.
 - **PartitionKind**. Схема секционирования службы. Это может иметь значение "Int64Range" (Диапазон Int64) или "Named" (Именованная). Этот параметр не является обязательным для служб, использующих схему одноэлементного секционирования.
 - **Timeout**. Время ожидания для HTTP-запроса к службе, созданного обратным прокси-сервером от имени клиентского запроса. По умолчанию это значение равно 60 секундам. Данный параметр является необязательным.

### Пример использования

Для примера рассмотрим службу **fabric:/MyApp/MyService**, которая открывает прослушиватель HTTP по следующему URL-адресу:

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

И с приведенными ниже ресурсами.

 - `/index.html`
 - `/api/users/<userId>`

Если служба использует схему одноэлементного секционирования, параметры строки запроса *PartitionKey* и *PartitionKind* можно не указывать и к службе можно обратиться через шлюз так:

 - Извне: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`.
 - Изнутри: `http://localhost:19008/MyApp/MyService`.

Если служба использует схему секционирования Uniform Int64, для обращения к секции службы необходимо использовать параметры строки запроса *PartitionKey* и *PartitionKind*.

 - Извне: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`.
 - Изнутри: `http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`.

Укажите путь к ресурсу после имени службы в URL-адресе, чтобы обратиться к предоставленным службой ресурсам.

 - Извне: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`.
 - Изнутри: `http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`.

Затем шлюз перешлет эти запросы по URL-адресу службы.

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## Специальные действия для служб с общим доступом к портам

Шлюз приложений пытается повторно разрешить адрес службы и повторить запрос, если служба недоступна. Это одно из основных преимуществ шлюза, так как в коде клиента не требуется реализовывать собственный цикл разрешения службы.

Обычно недоступность службы означает, что экземпляр службы или реплика была перемещена на другой узел в ходе обычного жизненного цикла. В этом случае шлюз может получить ошибку сетевого подключения, указывающую, что конечная точка больше не открыта по первоначально разрешенному адресу.

Тем не менее реплики или экземпляры службы могут совместно использовать хост-процесс или порт при размещении на веб-сервере на основе http.sys, включая:

 - [System.Net.HttpListener;](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [ASP.NET Core WebListener;](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Katana.](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

В этом случае вполне вероятно, что веб-сервер доступен в хост-процессе и отвечает на запросы, но разрешенный экземпляр службы или реплика больше не доступна на узле. В этом случае шлюз получит от веб-сервера ответ "HTTP 404". Ввиду вышесказанного ошибка "HTTP 404" может иметь два разных значения:

 1. Адрес службы указан правильно, но запрошенный пользователем ресурс не существует.
 2. Неправильный адрес службы, а ресурс, запрошенный пользователем, на самом деле может существовать на другом узле.

В первом случае это обычная ошибка "HTTP 404", которая считается ошибкой пользователя. Однако во втором случае пользователь запросил ресурс, который существует, но шлюзу не удалось найти его из-за перемещения самой службы. В этом случае шлюз должен повторно разрешить адрес и повторить попытку.

Таким образом, шлюзу необходим способ, позволяющий различать эти два случая. Для этого требуется указание от сервера.

 - По умолчанию шлюз приложений предполагает второй случай и пытается повторить разрешение адреса службы и отправку запроса.
 - Чтобы указать шлюзу приложений, что это первый случай, служба должна вернуть следующий заголовок ответа HTTP.

`X-ServiceFabric : ResourceNotFound`

Этот заголовок ответа HTTP указывает обычную ситуацию возникновения ошибки "HTTP 404", в которой запрошенный ресурс не существует, и шлюз не будет пытаться повторно разрешить адрес службы.

## Установка и настройка
Обратный прокси-сервер Service Fabric можно включить для кластера с помощью [шаблона Resource Manager](./service-fabric-cluster-creation-via-arm.md).

Получив шаблон для кластера, который вы хотите развернуть (из коллекции примеров или создав шаблон Resource Manager), вы можете включить в нем обратный прокси-сервера, выполнив следующие действия.

1. Определите порт обратного прокси-сервера в разделе [Parameters](../resource-group-authoring-templates.md) шаблона.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Укажите порт для каждого типа узлов в [разделе типов ресурсов](../resource-group-authoring-templates.md) в подразделе **Cluster**.

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Для обращения к обратному прокси-серверу извне кластера Azure настройте **правила Azure Load Balancer** для порта, указанного на шаге 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Чтобы настроить SSL-сертификаты для порта обратного прокси-сервера, добавьте сертификат в свойство httpApplicationGatewayCertificate. Это свойство можно найти в [разделе типов ресурсов](../resource-group-authoring-templates.md) в подразделе **Cluster**.

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## Дальнейшие действия
 - Пример HTTP-подключения между службами см. в [примере проекта на сайте GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Удаленное взаимодействие службы с Reliable Services](service-fabric-reliable-services-communication-remoting.md)

 - [Начало работы со службами веб-API Microsoft Azure Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md)

 - [Коммуникационный стек WCF для надежных служб](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png

<!---HONumber=AcomDC_0921_2016-->