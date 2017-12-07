---
title: "Создание устройства прозрачного шлюза с помощью Azure IoT Edge | Документация Майкрософт"
description: "Использование Azure IoT Edge для создания устройства прозрачного шлюза, которое может обрабатывать сведения для нескольких устройств"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: eaabf360eec48fad6bb1b8b889f30d746520eef0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2017
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Создание устройства IoT Edge, которое работает как прозрачный шлюз — предварительная версия

В статье [Использование устройства IoT Edge в качестве шлюза (предварительная версия)][lnk-edge-as-gateway] показано концептуальное представление о том, как можно использовать устройство IoT Edge в качестве прозрачного шлюза или выполнять преобразование протокола или удостоверения. В этой статье приведены подробные инструкции по использованию устройства IoT Edge в качестве прозрачного шлюза. В оставшейся части этой статьи под *шлюзом IoT Edge* подразумевается устройство IoT Edge, которое используется в качестве прозрачного шлюза.

>[!NOTE]
>В данный момент:
> * подчиненные устройства не могут пройти проверку подлинности с помощью шлюза, если шлюз не подключен к Центру Интернета вещей;
> * устройства IoT Edge не могут подключиться к шлюзам IoT Edge.

## <a name="use-the-azure-iot-device-sdk"></a>Использование пакета SDK для устройств Azure IoT


Концентратор Edge, установленный на всех устройствах IoT Edge, предоставляет следующие примитивы для подчиненных устройств:

* сообщения, передающиеся с устройств в облако и из облака на устройства;
* прямые методы;
* операции с двойником устройства.

>[!NOTE]
>Сейчас подчиненные устройства не могут передавать файлы при подключении через шлюз IoT Edge.

При подключении устройств к шлюзу IoT Edge с помощью пакета SDK для устройств Azure IoT вам понадобится:

* настроить подчиненное устройство со строкой подключения, ссылающейся на имя узла устройства шлюза;
* убедиться, что подчиненное устройство доверяет сертификату, используемому для принятия подключения к устройству шлюза.

При установке среды выполнения Azure IoT Edge с помощью скрипта управления для концентратора Edge создается сертификат, как описано в руководстве по установке IoT Edge на имитированном устройстве в [Windows][lnk-tutorial1-win] и [Linux][lnk-tutorial1-lin]. Этот сертификат используется в концентраторе Edge для принятия входящих подключений по протоколу TLS, а подчиненное устройство должно доверять ему при подключении к устройству шлюза.

Вы можете создать любую инфраструктуру сертификата, обеспечивающую доверие, необходимое для топологии "устройство — шлюз". В этой статье предполагается та же настройка сертификата, что и для включения [безопасности ЦС X.509][lnk-iothub-x509] в Центре Интернета вещей. Она включает в себя сертификат ЦС X.509, связанный с определенным Центром Интернета вещей (*ЦС владельца Центра Интернета вещей*), и ряд сертификатов, подписанных этим ЦС, установленных на устройствах IoT Edge.

>[!IMPORTANT]
>Сейчас для проверки подлинности с помощью Центра Интернета вещей устройства IoT Edge подчиненные устройства могут использовать только [маркеры безопасности SAS][lnk-iothub-tokens]. Сертификаты будут использоваться только для проверки подключения по протоколу TLS между конечным объектом и устройством шлюза.

В нашей конфигурации **ЦС владельца Центра Интернета вещей** используется в качестве:
* сертификата для подписи для настройки среды выполнения IoT Edge на всех устройствах IoT Edge;
* сертификата открытого ключа, установленного на подчиненных устройствах.

В результате этого мы имеем решение, благодаря которому все устройства могут использовать любое устройство IoT Edge в качестве шлюза при условии, что они подключены к одному Центру Интернета вещей.

### <a name="create-the-certificates-for-test-scenarios"></a>Создание сертификатов для тестовых сценариев

Вы можете использовать примеры скриптов Powershell и Bash, описанные в статье [Managing CA Certificates Sample][lnk-ca-scripts] (Пример управления сертификатами ЦС), для создания самозаверяющего **ЦС владельца Центра Интернета вещей** и сертификатов устройства, подписанных с его помощью.

1. Выполните шаг 1 из статьи [Managing CA Certificates Sample][lnk-ca-scripts] (Пример управления сертификатами ЦС), чтобы установить скрипты. Обязательно выполните клонирование из ветви `modules-preview`.
                
                git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
2. Выполните шаг 2, чтобы создать **ЦС владельца Центра Интернета вещей**. Этот файл будут использовать подчиненные устройства для проверки подключения.

Используйте следующие инструкции, чтобы создать сертификат для устройства шлюза.

#### <a name="bash"></a>Bash

* Выполните `./certGen.sh create_edge_device_certificate myGateway`, чтобы создать сертификат устройства.  
  Будут созданы файлы .\certs\new-edge-device.*, содержащие открытый ключ и PFX-файл, и .\private\new-edge-device.key.pem, содержащий закрытый ключ устройства.  
* В каталоге `certs` выполните команду `cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem`, чтобы получить полную цепочку открытого ключа устройства.
* `./private/new-edge-device.cert.pem` содержит закрытый ключ устройства.

#### <a name="powershell"></a>PowerShell

* Выполните `New-CACertsEdgeDevice myGateway`, чтобы создать сертификат устройства.
  Будут созданы файлы myEdgeDevice*, содержащие открытый и закрытый ключ и PFX-файл сертификата.  При появлении запроса на ввод пароля в процессе подписания введите "1234".


>[!IMPORTANT]
>Этот образец предназначен только для целей тестирования. Для рабочих сценариев инструкции для Azure IoT о том, как защитить ваше решение Интернета вещей и соответствующим образом подготовить свой сертификат, см. в статье [Защита развертывания IoT][lnk-iothub-secure-deployment].

### <a name="configure-an-iot-edge-device-as-a-gateway"></a>Настройка устройства IoT Edge в качестве шлюза

Чтобы настроить устройство IoT Edge в качестве шлюза, необходимо настроить использование сертификата устройства, созданного в предыдущем разделе.

Предполагается, что используются следующие имена файлов из приведенных выше примеров скриптов:

| Выходные данные | Сценарий Bash | PowerShell |
| ------ | ----------- | ---------- |
| Сертификат устройства | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Закрытый ключ устройства | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Цепочка сертификатов устройства | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| ЦС владельца Центра Интернета вещей | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

 Аналогично установке, описанной в статье о развертывании Azure IoT Edge на имитированном устройстве в [Windows][lnk-tutorial1-win] или [Linux][lnk-tutorial1-lin], вам нужно предоставить указанные выше сведения для среды выполнения IoT Edge. 
 
 В Linux:

        sudo iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem

В Windows:

        iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/RootCA.pem

По умолчанию примеры скриптов не задают парольную фразу в закрытом ключе устройства. Если парольная фраза задана, добавьте следующий параметр:

        --device-ca-passphrase {passphrase}

Скрипт предложит установить парольную фразу для сертификата агента Edge.
После этой команды необходимо перезагрузить среду выполнения IoT Edge.

### <a name="configure-an-iot-hub-device-application-as-a-downstream-device"></a>Настройка приложения устройства Центра Интернета вещей в качестве подчиненного устройства

Подчиненное устройство можно сделать любым приложением с помощью [пакета SDK для устройств Azure IoT][lnk-devicesdk]. Пример такого простого приложения описан в статье [Подключение устройства к Центру Интернета вещей с помощью .NET][lnk-iothub-getstarted].

Для начала приложение подчиненного устройства должно доверять сертификату **ЦС владельца Центра Интернета вещей** для проверки подключений по протоколу TLS к устройствам шлюза. Как правило, это действие можно выполнить двумя способами: на уровне ОС или на уровне приложения (для некоторых языков).

Например, для приложений .NET можно добавить следующий фрагмент кода, чтобы доверять сертификату в PEM-формате, сохраненному в пути `certPath`. Если используется приведенный выше сценарий, то в пути будет указан `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) или `RootCA.pem` (PowerShell).

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

Выполнение этого действия на уровне ОС отличается в разных версиях Windows и дистрибутивах Linux.

Второй шаг — инициализировать пакет SDK для устройств Центра Интернета вещей со строкой подключения, ссылающейся на имя узла устройства шлюза.
Это можно сделать, добавив свойство `GatewayHostName` в строку подключения устройства. Например, ниже приведен пример строки подключения устройства для устройства, к которому добавляется свойство `GatewayHostName`:

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

Эти два действия позволят приложению устройства подключиться к устройству шлюза.

## <a name="next-steps"></a>Дальнейшие действия
[Understand the requirements and tools for developing IoT Edge modules - preview][lnk-module-dev] (Сведения о требованиях и средствах разработки модулей IoT Edge (предварительная версия)).

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus