---
title: Защищенное взаимодействие с обратным прокси-сервером Azure Service Fabric | Документы Майкрософт
description: Настройка обратного прокси-сервера для обеспечения безопасного сквозного взаимодействия.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 237a72fd282b29d3032675ccf3fb350f8db59ef7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209177"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Подключение к защищенной службе с помощью обратного прокси-сервера

В этой статье объясняется, как установить безопасное подключение между обратным прокси-сервером и службами, которое будет использоваться в качестве защищенного сквозного канала.

Подключение к защищенным службам поддерживается, только если обратный прокси-сервер настроен для прослушивания по протоколу HTTPS. В остальной части документа предполагается, что это так.
Сведения о настройке обратного прокси-сервера Service Fabric см. в статье [Обратный прокси-сервер в Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Установление безопасного подключения между обратным прокси-сервером и службами 

### <a name="reverse-proxy-authenticating-to-services"></a>Обратный прокси-сервер проходит аутентификацию в службах
Обратный прокси-сервер должен идентифицировать себя в службах с помощью сертификата, заданного свойством ***reverseProxyCertificate*** в [разделе "Тип ресурса"](../azure-resource-manager/resource-group-authoring-templates.md) **кластера**. Службы могут реализовать логику для проверки сертификата, представленного обратным прокси-сервером. Службы могут указывать сведения о принятом сертификате клиента как параметры конфигурации в пакете конфигурации. Их можно считывать во время выполнения и использовать для проверки сертификата, представленного обратным прокси-сервером. Сведения о добавлении параметров конфигурации см. в статье [Управление параметрами приложения](service-fabric-manage-multiple-environment-app-configuration.md). 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Проверка обратным прокси-сервером удостоверения службы с помощью сертификата, предоставленного службой
Для выполнения проверки сертификатов, представленных службами, обратный прокси-сервер поддерживает один из следующих параметров: None, ServiceCommonNameAndIssuer и ServiceCertificateThumbprints.
Чтобы выбрать один из этих трех параметров, задайте свойство **ApplicationCertificateValidationPolicy** в разделе параметров элемента ApplicationGateway/Http в [fabricSettings](service-fabric-cluster-fabric-settings.md).

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "None"
              }
            ]
          }
        ],
        ...
}
```

Сведения о дополнительной настройке для каждого из этих параметров см. в следующем разделе.

### <a name="service-certificate-validation-options"></a>Параметры проверки сертификата службы 

- **None**: обратный прокси-сервер пропускает проверку сертификата службы, подключаемой через прокси-сервер, и устанавливает безопасное подключение. Это поведение по умолчанию.
Задайте для свойства **ApplicationCertificateValidationPolicy** значение **None** в разделе параметров элемента ApplicationGateway/Http.

- **ServiceCommonNameAndIssuer**: обратный прокси-сервер проверяет сертификат, представленный службой, на основании общего имени сертификата и отпечатка непосредственного издателя. Задайте для свойства **ApplicationCertificateValidationPolicy** значение **ServiceCommonNameAndIssuer** в разделе параметров элемента ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCommonNameAndIssuer"
              }
            ]
          }
        ],
        ...
}
```

Чтобы указать список общих имен служб и отпечатков издателей, добавьте элемент **ApplicationGateway/Http/ServiceCommonNameAndIssuer** в раздел fabricSettings, как показано ниже. В элемент массива параметров можно добавить несколько пар общих имен сертификатов и отпечатков издателей. 

Если обратный прокси-сервер конечной точки подключается для представления сертификата, общее имя и отпечаток издателя которого соответствуют любому из указанных в элементе значений, устанавливается SSL-канал. Если соответствие для сведений о сертификате не найдено, обратный прокси-сервер не выполняет запрос клиента с кодом состояния 502 (неверный шлюз). В строке состояния HTTP также будет содержаться фраза "Недопустимый сертификат SSL". 

```json
{
"fabricSettings": [
          ...
          {
             "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
            "parameters": [
              {
                "name": "WinFabric-Test-Certificate-CN1",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
              },
              {
                "name": "WinFabric-Test-Certificate-CN2",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
              }
            ]
          }
        ],
        ...
}
```


- **ServiceCertificateThumbprints**: обратный прокси-сервер будет проверять сертификат службы на основе отпечатка. Вы можете выбрать этот вариант, если для служб настроены самозаверяющие сертификаты: задайте для свойства **ApplicationCertificateValidationPolicy** значение **ServiceCertificateThumbprints** в разделе параметров элемента ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCertificateThumbprints"
              }
            ]
          }
        ],
        ...
}
```

Также укажите отпечатки с помощью записи **ServiceCertificateThumbprints** в разделе параметров элемента ApplicationGateway/Http. Можно указать несколько отпечатков в виде списка с разделителями-запятыми в поле значения, как показано ниже:

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "ServiceCertificateThumbprints",
                "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
              }
            ]
          }
        ],
        ...
}
```

Если отпечаток сертификата сервера содержится в этой записи конфигурации, обратный прокси-сервер успешно устанавливает SSL-подключение. В противном случае — он завершает подключение и не выполняет запроса клиента с ошибкой 502 (неверный шлюз). В строке состояния HTTP также будет содержаться фраза "Недопустимый сертификат SSL".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Логика выбора конечной точки, когда службы предоставляют защищенные и незащищенные конечные точки
Service Fabric поддерживает настройку нескольких конечных точек для службы. См. [Указание ресурсов в манифесте службы](service-fabric-service-manifest-resources.md).

Обратный прокси-сервер выбирает одну из конечных точек для пересылки запросов в зависимости от значения параметра запроса **ListenerName**. Если этот параметр не указан, сервер может выбрать любую конечную точку из списка конечных точек. Это может быть конечная точка HTTP или HTTPS. Существует ряд сценариев и требований, когда обратный прокси-сервер должен работать "только в безопасном режиме", т. е. защищенный обратный прокси-сервер не должен пересылать запросы в незащищенные конечные точки. Это можно сделать, указав запись конфигурации **SecureOnlyMode** со значением **true** в разделе параметров элемента ApplicationGateway/Http.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> 
> При работе в режиме **SecureOnlyMode**, если клиент указал значение **ListenerName**, соответствующее конечной точке HTTP (незащищенной), обратный прокси-сервер не выполняет запрос с кодом состояния HTTP 404 (не найдено).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Настройка проверки подлинности сертификата клиента через обратный прокси-сервер
Разрыв SSL-подключения выполняется на обратном прокси-сервере, и все данные сертификата клиента утрачиваются. Чтобы службы могли выполнять проверку подлинности сертификата клиента, задайте параметр **ForwardClientCertificate** в разделе параметров элемента ApplicationGateway/Http.

1. Если для параметра **ForwardClientCertificate** задано значение **false**, обратный прокси-сервер не будет запрашивать сертификат клиента во время подтверждения SSL.
Это поведение по умолчанию.

2. Если для параметра **ForwardClientCertificate** задано значение **true**, обратный прокси-сервер запрашивает сертификат клиента во время подтверждения SSL.
Затем он пересылает данные сертификата клиента в пользовательский заголовок HTTP с именем **X-Client-Certificate**. Значение заголовка является строкой формата PEM в кодировке base64 сертификата клиента. Служба может успешно или неудачно выполнить запрос с соответствующим кодом состояния после проверки данных сертификата.
Если клиент не представляет сертификат, обратный прокси-сервер пересылает пустой заголовок и передает принятие решения службе.

> Обратный прокси-сервер является простым сервером пересылки. Он не выполняет проверку сертификата клиента.


## <a name="next-steps"></a>Дополнительная информация
* Примеры шаблонов Azure Resource Manager для настройки разных параметров проверки сертификата службы для защищенного обратного прокси-сервера см. в разделе [Configure reverse proxy to connect to secure services](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services).
* Пример обмена данными по протоколу HTTP между службами представлен в [примере проекта на сайте GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Удаленное взаимодействие службы с Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Начало работы со службами веб-API Microsoft Azure Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md)
* [Управление сертификатами кластера](service-fabric-cluster-security-update-certs-azure.md)
