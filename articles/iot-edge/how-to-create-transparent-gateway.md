---
title: Создание устройства прозрачного шлюза с помощью Azure IoT Edge | Документация Майкрософт
description: Использование Azure IoT Edge для создания устройства прозрачного шлюза, которое может обрабатывать сведения для нескольких устройств
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0378cb2964a496a2bfe5a0bc08296cbab462a409
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170716"
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Создание устройства IoT Edge, которое работает как прозрачный шлюз — предварительная версия

В этой статье приведены подробные инструкции по использованию устройства IoT Edge в качестве прозрачного шлюза. В оставшейся части статьи под термином *шлюз IoT Edge* подразумевается устройство IoT Edge, которое используется в качестве прозрачного шлюза. Дополнительные сведения см. в документации по [использованию устройства IoT Edge в качестве шлюза][lnk-edge-as-gateway]. 

>[!NOTE]
>В данный момент:
> * если шлюз не подключен к Центру Интернета вещей, подчиненные устройства не смогут выполнить аутентификацию с помощью шлюза;
> * устройства IoT Edge не могут подключиться к шлюзам IoT Edge.

## <a name="understand-the-azure-iot-device-sdk"></a>Общие сведения о пакете SDK для устройств Azure IoT


Концентратор Edge, установленный на всех устройствах IoT Edge, предоставляет следующие примитивы для подчиненных устройств:

* сообщения, которые отправляются с устройств в облако и из облака на устройства;
* прямые методы;
* операции с двойником устройства.

Сейчас подчиненные устройства не могут передавать файлы при подключении через шлюз IoT Edge.

При подключении устройств к шлюзу IoT Edge с помощью пакета SDK для устройств Azure IoT вам понадобится:

* настроить подчиненное устройство со строкой подключения, ссылающейся на имя узла устройства шлюза;
* убедиться, что подчиненное устройство доверяет сертификату, используемому для принятия подключения к устройству шлюза.

Когда вы устанавливаете среду выполнения Azure IoT Edge с помощью скрипта управления, для концентратора Edge создается сертификат (см. руководство по установке IoT Edge на имитированном устройстве в [Windows][lnk-tutorial1-win] и [Linux][lnk-tutorial1-lin]). Этот сертификат используется в концентраторе Edge для принятия входящих подключений по протоколу TLS, а подчиненное устройство должно доверять ему при подключении к устройству шлюза.

Вы можете создать любую инфраструктуру сертификата, обеспечивающую доверие, необходимое для топологии "устройство — шлюз". В этой статье используется та же конфигурация сертификата, что и для включения в Центре Интернета вещей [защиты с помощью сертификата ЦС X.509][lnk-iothub-x509], который связан с определенным Центром Интернета вещей (*ЦС владельца Центра Интернета вещей*), и ряда сертификатов, подписанных этим ЦС и установленных на устройствах IoT Edge.

>[!IMPORTANT]
>Сейчас для проверки подлинности с помощью Центра Интернета вещей устройства IoT Edge подчиненные устройства могут использовать только [маркеры безопасности SAS][lnk-iothub-tokens]. Сертификаты будут использоваться только для проверки подключения по протоколу TLS между конечным объектом и устройством шлюза.

В нашей конфигурации **ЦС владельца Центра Интернета вещей** используется в качестве:
* сертификата для подписи, используемого при настройке среды выполнения IoT Edge на всех устройствах IoT Edge;
* сертификата открытого ключа, установленного на подчиненных устройствах.

В результате мы имеем решение, благодаря которому все устройства могут использовать любое устройство IoT Edge в качестве шлюза при условии, что они подключены к одному Центру Интернета вещей.

## <a name="create-the-certificates-for-test-scenarios"></a>Создание сертификатов для тестовых сценариев

Вы можете использовать примеры скриптов Powershell и Bash, описанные в статье [Managing CA Certificates Sample][lnk-ca-scripts] (Пример управления сертификатами ЦС), для создания самозаверяющего **ЦС владельца Центра Интернета вещей** и сертификатов устройства, подписанных с его помощью.

>[!IMPORTANT]
>Этот образец предназначен только для целей тестирования. Для рабочих сценариев инструкции для Azure IoT о том, как защитить ваше решение Интернета вещей и соответствующим образом подготовить свой сертификат, см. в статье [Защита развертывания IoT][lnk-iothub-secure-deployment].


1. Клонируйте из репозитория GitHub пакеты SDK для Microsoft Azure IoT и библиотеки для C:

   ```cmd/sh
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. Выполните **шаг 1 (начальная настройка)** из руководства с [примером управления сертификатами ЦС][lnk-ca-scripts],чтобы установить скрипты сертификата. 
3. Выполните **шаг 2 (создание цепочки сертификатов)**, чтобы создать **ЦС владельца Центра Интернета вещей**. Этот файл используется подчиненными устройствами для проверки подключения.
4. Чтобы создать сертификат для устройства шлюза, используйте инструкции Bash или PowerShell:

### <a name="bash"></a>Bash

Создайте сертификат устройства.  Имя `myGatewayCAName` **НЕ ДОЛЖНО**  совпадать с именем узла шлюза.  Это приведет к ошибке сертификации клиентов для этих сертификатов.

   ```bash
   ./certGen.sh create_edge_device_certificate myGatewayCAName
   ```

Будут созданы новые файлы: 1) .\certs\new-edge-device.*, содержащий открытый ключ и PFX-файл; 2) .\private\new-edge-device.key.pem, содержащий закрытый ключ устройства.
 
Чтобы получить полную цепочку открытого ключа устройства, в каталоге `certs` выполните следующую команду:

   ```bash
   cd ./certs
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>PowerShell

Создайте сертификат устройства: 
   ```powershell
   New-CACertsEdgeDevice myGateway
   ```

Будут созданы новые файлы myEdgeDevice*, содержащие открытый и закрытый ключи, а также PFX-файл этого сертификата. 

При появлении запроса на ввод пароля в процессе подписания введите "1234".

## <a name="configure-a-gateway-device"></a>Настройка устройства шлюза

Чтобы настроить устройство IoT Edge в качестве шлюза, необходимо настроить использование сертификата устройства, созданного в предыдущем разделе.

Предполагается, что используются следующие имена файлов из приведенных выше примеров скриптов:

| Выходные данные | Имя файла |
| ------ | --------- |
| Сертификат устройства | `certs/new-edge-device.cert.pem` |
| Закрытый ключ устройства | `private/new-edge-device.cert.pem` |
| Цепочка сертификатов устройства | `certs/new-edge-device-full-chain.cert.pem` |
| ЦС владельца Центра Интернета вещей | `certs/azure-iot-test-only.root.ca.cert.pem`  |

Укажите сведения об устройстве и сертификате для среды выполнения IoT Edge. 
 
В Linux используйте выходные данные Bash:

   ```bash
   sudo iotedgectl setup --connection-string {device connection string} \
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com} \
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem \
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem \
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem \
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

В Windows используйте выходные данные PowerShell:

   ```powershell
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

По умолчанию примеры скриптов не задают парольную фразу в закрытом ключе устройства. Если парольная фраза задана, добавьте следующий параметр: `--device-ca-passphrase {passphrase}`.

Скрипт предложит установить парольную фразу для сертификата агента Edge. После выполнения этой команды необходимо перезагрузить среду выполнения IoT Edge:

   ```cmd/sh
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>Настройка подчиненного устройства

Подчиненное устройство можно сделать любым приложением с помощью [пакета SDK для устройств Azure IoT][lnk-devicesdk]. Пример такого простого приложения описан в статье [Подключение устройства к Центру Интернета вещей с помощью .NET][lnk-iothub-getstarted].

Для начала приложение подчиненного устройства должно доверять сертификату **ЦС владельца Центра Интернета вещей** для проверки подключений по протоколу TLS к устройствам шлюза. Как правило, это действие можно выполнить двумя способами: на уровне ОС или на уровне приложения (для некоторых языков).

Например, для приложений .NET можно добавить следующий фрагмент кода, чтобы доверять сертификату в PEM-формате, сохраненному в пути `certPath`. В зависимости от версии используемого скрипта, путь будет содержать либо `certs/azure-iot-test-only.root.ca.cert.pem` (Bash), либо `RootCA.pem` (Powershell).

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

Выполнение этого действия на уровне ОС отличается в разных версиях Windows и дистрибутивах Linux.

Второй шаг — инициализировать пакет SDK для устройств Центра Интернета вещей со строкой подключения, ссылающейся на имя узла устройства шлюза.
Это можно сделать, добавив свойство `GatewayHostName` в строку подключения устройства. Например, ниже приведен пример строки подключения устройства для устройства, к которому добавляется свойство `GatewayHostName`:

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

Эти два действия позволят приложению устройства подключиться к устройству шлюза.

## <a name="next-steps"></a>Дополнительная информация
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
