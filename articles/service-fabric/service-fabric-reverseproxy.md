---
title: Обратный прокси-сервер Azure Service Fabric | Документация Майкрософт
description: Использование обратного прокси-сервера Service Fabric для взаимодействия с микрослужбами изнутри и извне кластера.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 21e1e3041d7b1f4dc205355f6c0b8d4fd2e82775
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212281"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Обратный прокси-сервер в Azure Service Fabric
Обратный прокси-сервер, встроенный в Azure Service Fabric, помогает микрослужбам, работающим в кластере Service Fabric, обнаруживать другие службы с конечными точками HTTP и взаимодействовать с этими службами.

## <a name="microservices-communication-model"></a>Модель взаимодействия с микрослужбами
Микрослужбы в Service Fabric выполняются на подмножестве узлов в кластере и по различным причинам могут перемещаться между ними. Поэтому конечные точки для микрослужб могут динамически изменяться. Чтобы обнаружить другие службы в кластере и взаимодействовать с ними, микрослужбам требуется выполнить следующие действия:

1. Разрешение расположения службы через службу именования.
2. Подключение к службе.
3. Заключение описанных выше шагов в цикл, который реализует разрешение службы и политики повтора при сбое подключения.

Дополнительные сведения см. в разделе [Подключение к службам в Service Fabric и взаимодействие с ними](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Обмен данными с использованием обратного прокси-сервера
Обратный прокси-сервер является службой, которая работает на каждом узле и обрабатывает разрешение конечных точек, автоматический повтор операций и другие сбои подключения от имени служб клиента. Обратный прокси-сервер можно настроить для применения различных политик при обработке запросов от служб клиента. Пи использовании обратного прокси-сервера служба клиента может использовать любые клиентские библиотеки связи HTTP и не требует специального разрешения и логики повтора в службе. 

Обратный прокси-сервер предоставляет службам клиента одну или несколько конечных точек на локальном узле для отправки запросов к другим службам.

![Взаимодействие изнутри][1]

> [!NOTE]
> **Поддерживаемые платформы**
>
> Обратный прокси-сервер в Service Fabric в настоящее время поддерживает следующие платформы:
> * *Кластер Windows*: Windows 8 и более поздней версии или Windows Server 2012 и более поздней версии;
> * *Кластер Linux*: сейчас обратный прокси-сервер для кластеров Linux недоступен.
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Обращение к микрослужбам извне кластера
По умолчанию микрослужбы для внешнего взаимодействия используют модель участия: к любой службе нельзя получить доступ напрямую из внешних клиентов. [Azure Load Balancer](../load-balancer/load-balancer-overview.md) — это граница сети между микрослужбами и внешними клиентами, которая выполняет преобразование сетевых адресов и пересылает внешние запросы к внутренним конечным точкам "IP-адрес:порт". Чтобы внешние клиенты могли напрямую обращаться к конечной точке, нужно сначала настроить подсистему балансировки нагрузки для пересылки трафика каждого порта, используемого службой в кластере. Более того, большая часть микрослужб, особенно микрослужбы с отслеживанием состояния, не выполняется на всех узлах кластера. Микрослужбы могут перемещаться между узлами при отработке отказа. В таких случаях подсистема балансировки нагрузки не может эффективно определить расположение целевого узла реплик, к которым следует пересылать трафик.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Обращение к микрослужбам через обратный прокси-сервер извне кластера
Вместо того, чтобы настраивать порт отдельной службы в подсистеме балансировки нагрузки, в ней можно настроить порт обратного прокси-сервера. Такая конфигурация позволит клиентам, расположенным за пределами кластера, обращаться к службам внутри него через обратный прокси-сервер без дополнительных настроек.

![Взаимодействие извне][0]

> [!WARNING]
> Настройка порта обратного прокси-сервера в подсистеме балансировки нагрузки обеспечит адресацию извне кластера всех микрослужб в этом кластере, которые предоставляют конечную точку HTTP.
>
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Формат универсального кода ресурса (URI) для адресации служб через обратный прокси-сервер
Обратный прокси-сервер использует определенный формат универсального кода ресурса (URI), чтобы определять, в какую секцию службы следует перенаправить входящий запрос.

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s).** Обратный прокси-сервер можно настроить для приема трафика HTTP или HTTPS. После настройки обратного прокси-сервера для прослушивания по протоколу HTTPS ознакомьтесь со сведениями о переадресации HTTPS в статье [Подключение к безопасной службе с помощью обратного прокси-сервера](service-fabric-reverseproxy-configure-secure-communication.md).
* **Cluster FQDN | internal IP.** Для внешних клиентов обратный прокси-сервер можно настроить таким образом, чтобы он был доступен через домен кластера (например, mycluster.eastus.cloudapp.azure.com). По умолчанию обратный прокси-сервер выполняется на каждом узле. Для внутреннего трафика он может быть доступен на узле localhost или по IP-адресу любого внутреннего узла (например, 10.0.0.1).
* **Port.** Порт, например 19081, указанный для обратного прокси-сервера.
* **ServiceInstanceName.** Полное имя развернутого экземпляра службы, к которому вы пытаетесь получить доступ, без использования схемы fabric:/. Например, чтобы подключиться к службе *fabric:/myapp/myservice/*, используется имя *myapp/myservice*.

    В имени экземпляра службы учитывается регистр. Использование символов разного регистра в имени экземпляра службы в URL-адресе приводит к сбою запросов с ошибкой "404 (не найдено)".
* **Suffix path**. Фактический URL-адрес службы, к которой вы подключаетесь, например *myapi/values/add/3*.
* **PartitionKey.** Для секционированной службы это вычисляемый ключ секции, к которой вы подключаетесь. Обратите внимание, что это *не* идентификатор GUID секции. Этот параметр не является обязательным для служб, использующих схему одноэлементного секционирования.
* **PartitionKind.** Схема секционирования службы. Это может иметь значение "Int64Range" (Диапазон Int64) или "Named" (Именованная). Этот параметр не является обязательным для служб, использующих схему одноэлементного секционирования.
* **ListenerName.** Конечные точки, представляемые службой, имеют следующий вид: {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}} Если служба представляет несколько конечных точек, то данный параметр определяет, к которой из них следует перенаправить клиентский запрос. При наличии только одного прослушивателя потребность в данном параметре отсутствует.
* **TargetReplicaSelector.** Данный параметр определяет, каким образом должна быть выбрана целевая реплика или экземпляр.
  * Если целевая служба является службой с отслеживанием состояния, то параметр TargetReplicaSelector может иметь значение PrimaryReplica, RandomSecondaryReplica или RandomReplica. Если этот параметр не указан, по умолчанию используется значение PrimaryReplica.
  * Если целевая служба является службой без отслеживания состояния, обратный прокси-сервер выбирает случайный экземпляр раздела службы, к которому направляется запрос.
* **Timeout.** Время ожидания HTTP-запроса к службе, созданного обратным прокси-сервером от имени клиентского запроса. Значение по умолчанию составляет 60 секунд. Данный параметр является необязательным.

### <a name="example-usage"></a>Пример использования
Для примера рассмотрим службу *fabric:/MyApp/MyService*, которая открывает прослушиватель HTTP по приведенному ниже URL-адресу.

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Ниже приведены ресурсы для службы.

* `/index.html`
* `/api/users/<userId>`

Если служба использует схему одноэлементного секционирования, то параметры строки запроса *PartitionKey* и *PartitionKind* можно не указывать и к службе можно обратиться через шлюз следующим образом.

* Извне: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Изнутри: `http://localhost:19081/MyApp/MyService`

Если служба использует схему секционирования Uniform Int64, для обращения к секции службы необходимо использовать параметры строки запроса *PartitionKey* и *PartitionKind*.

* Извне: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Изнутри: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Укажите путь к ресурсу после имени службы в URL-адресе, чтобы обратиться к предоставленным службой ресурсам.

* Извне: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Изнутри: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`.

Затем шлюз перешлет эти запросы по URL-адресу службы.

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Специальные действия для служб с общим доступом к портам
Обратный прокси-сервер Service Fabric пытается повторно разрешить адрес службы и повторить запрос, если служба недоступна. Как правило, недоступность службы означает, что экземпляр или реплика службы была перемещена на другой узел в ходе обычного жизненного цикла. В этом случае обратный прокси-сервер может получить ошибку сетевого подключения, указывающую, что конечная точка больше не открыта по первоначально разрешенному адресу.

Тем не менее реплики или экземпляры службы могут совместно использовать хост-процесс и даже порт при размещении на веб-сервере на основе http.sys, включая:

* [System.Net.HttpListener;](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener;](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana.](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

В этом случае вполне вероятно, что веб-сервер доступен в хост-процессе и отвечает на запросы, но разрешенный экземпляр или реплика службы больше не доступна на узле. В этом случае шлюз получит от веб-сервера ответ "HTTP 404". Таким образом, ответ "HTTP 404" может иметь два различных значения:

- Случай № 1. Адрес службы указан правильно, но запрошенный пользователем ресурс не существует.
- Случай № 2. Адрес службы указан неправильно, а запрошенный пользователем ресурс может существовать на другом узле.

В первом случае это обычная ошибка "HTTP 404", которая считается ошибкой пользователя. Однако во втором случае пользователь запросил ресурс, который существует. Обратному прокси-серверу не удалось найти его, так как была перемещена сама служба. Обратному прокси-серверу необходимо еще раз разрешить адрес и повторить запрос.

Таким образом, обратному прокси-серверу необходим способ, позволяющий различать эти два случая. Для этого требуется указание от сервера.

* По умолчанию обратный прокси-сервер предполагает, что произошел второй случай, и пытается повторить разрешение адреса службы и отправку запроса.
* Чтобы указать обратному прокси-серверу, что это первый случай, служба должна вернуть следующий заголовок ответа HTTP:

  `X-ServiceFabric : ResourceNotFound`

Этот заголовок ответа HTTP указывает обычную ситуацию возникновения ошибки "HTTP 404", в которой запрошенный ресурс не существует, и обратный прокси-сервер не будет пытаться повторно разрешить адрес службы.

## <a name="setup-and-configuration"></a>Установка и настройка

### <a name="enable-reverse-proxy-via-azure-portal"></a>Включение обратного прокси-сервера на портале Azure

Во время создания нового кластера Service Fabric на портале Azure можно включить обратный прокси-сервер.
Во время **создания кластера Service Fabric** на шаге 2 ("Конфигурация кластера" > "Конфигурация типа узла") установите флажок возле параметра "Включить обратный прокси-сервер".
Для настройки безопасного обратного прокси-сервера можно указать SSL-сертификат на шаге 3 ("Безопасность" > "Настройка параметров безопасности кластера"): установите флажок возле параметра "Включить SSL-сертификат для обратного прокси-сервера" и введите сведения о сертификате.

### <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Включение обратного прокси-сервера с помощью шаблонов Azure Resource Manager

Обратный прокси-сервер можно включить для кластера в Service Fabric с помощью [шаблона Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

В статье [Configure HTTPS Reverse Proxy in a secure cluster](https://github.com/ChackDan/Service-Fabric/tree/master/ARM Templates/ReverseProxySecureSample#configure-https-reverse-proxy-in-a-secure-cluster) (Настройка обратного прокси-сервера HTTPS в защищенном кластере) приведены примеры шаблонов Azure Resource Manager для настройки защищенного обратного прокси-сервера с сертификатом и обработки смены сертификатов.

Сначала следует получить шаблон для кластера, который требуется развернуть. Можно использовать примеры шаблонов или создать пользовательский шаблон Resource Manager. Затем можно включить обратный прокси-сервер, выполнив следующие действия.

1. Определите порт обратного прокси-сервера в разделе [Parameters](../azure-resource-manager/resource-group-authoring-templates.md) шаблона.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Укажите порт для каждого типа узлов в подразделе [Cluster](../azure-resource-manager/resource-group-authoring-templates.md) **раздела типов ресурсов**.

    Порт идентифицируется по имени параметра reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Для обращения к обратному прокси-серверу извне кластера Azure настройте правила Azure Load Balancer для порта, указанного на шаге 1.

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
4. Чтобы настроить SSL-сертификаты для порта обратного прокси-сервера, добавьте сертификат в свойство ***reverseProxyCertificate*** в подразделе **Cluster** [раздела типов ресурсов](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Поддержка сертификата обратного прокси-сервера, отличного от сертификата кластера
 Если сертификат обратного прокси-сервера отличается от сертификата, который защищает кластер, то указанный ранее сертификат необходимо установить на виртуальную машину и добавить в список управления доступом (ACL), чтобы предоставить Service Fabric к нему доступ. Для этого вы можете использовать **virtualMachineScaleSets** в [разделе типов ресурсов](../resource-group-authoring-templates.md). Чтобы установить сертификат, добавьте его в osProfile. Раздел extension шаблона позволяет обновить сертификат в списке управления доступом.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Чтобы в существующем кластере включить обратный прокси-сервер с сертификатом, отличающимся от сертификата кластера, необходимо сначала установить сертификат обратного прокси-сервера и обновить список управления доступом в кластере. Выполните развертывание [шаблона Azure Resource Manager](service-fabric-cluster-creation-via-arm.md), используя описанные выше параметры, прежде чем начинать развертывание для включения обратного прокси-сервера с помощью шагов 1–4.

## <a name="next-steps"></a>Дополнительная информация
* Пример обмена данными по протоколу HTTP между службами представлен в [примере проекта на сайте GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Подключение к безопасной службе с помощью обратного прокси-сервера](service-fabric-reverseproxy-configure-secure-communication.md)
* [Удаленное взаимодействие службы с Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Начало работы со службами веб-API Microsoft Azure Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md)
* [Коммуникационный стек WCF для надежных служб](service-fabric-reliable-services-communication-wcf.md)
* Дополнительные параметры конфигурации обратного прокси-сервера описаны в разделе о ApplicationGateway/Http статьи [Настройка параметров кластера Service Fabric и политики обновления структур](service-fabric-cluster-fabric-settings.md).

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
