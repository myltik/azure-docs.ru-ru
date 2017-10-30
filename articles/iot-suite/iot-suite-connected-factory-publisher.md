---
title: "Использование издателя OPC подключенной фабрики Azure IoT Suite | Документация Майкрософт"
description: "Сведения о сборке и развертывании эталонной реализации издателя OPC подключенной фабрики."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: dobett
ms.openlocfilehash: fd823194f6e51600b9d4ca1daa053db837871fef
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2017
---
# <a name="opc-publisher-for-azure-iot-edge"></a>Издатель OPC для Azure IoT Edge

В этой статье описывается использование эталонной реализации издателя OPC. Эталонная реализация демонстрирует использование Azure IoT Edge для:

- подключения к имеющимся серверам на основе унифицированной архитектуры (UA) OPC;
- публикации данных телеметрии в кодировке JSON из этих серверов в формате *публикация/подписка* OPC UA с использованием полезных данных JSON в Центре Интернета вещей. Можно использовать любой из транспортных протоколов, поддерживаемых Azure IoT Edge.

Это эталонное приложение включает в себя:

- *клиент* OPC UA для подключения к имеющимся серверам OPC UA в сети;
- *сервер* OPC UA прослушивает порт 62222, который можно использовать для управления публикациями.

Приложение реализовано на базе .NET Core и может выполняться на платформах, поддерживаемых в .NET Core.

Издатель реализует логику повторных попыток при установке соединения с конечными точками. Издатель ожидает, что конечные точки ответят в рамках определенного количества активных запросов. Благодаря такой логике издатель может обнаруживать разные состояния, например отключение питания сервера OPC UA.

Для каждого отдельного интервала публикации для сервера OPC UA создается отдельная подписка, в которой все узлы в этом интервале публикации обновляются.

Чтобы снизить нагрузку на сеть, издатель поддерживает пакетную обработку данных, отправленных в Центр Интернета вещей. Пакет отправляется в Центр Интернета вещей, только если достигнут настроенный максимальный размер пакета.

Это приложение использует эталонный стек OPC UA организации OPC, поэтому применяются ограничения лицензирования. Посетите страницу http://opcfoundation.github.io/UA-.NETStandardLibrary/, чтобы ознакомиться с документацией и условиями лицензирования OPC UA.

Исходный код издателя OPC можно найти в репозитории GitHub [издателя OPC для Azure IoT Edge](https://github.com/Azure/iot-edge-opc-publisher).

## <a name="prerequisites"></a>Предварительные требования

Чтобы создать приложение, необходим [пакет SDK для .NET Core 1.1.](https://docs.microsoft.com/dotnet/core/sdk) для операционной системы.

## <a name="build-the-application"></a>Создание приложения

### <a name="as-native-windows-application"></a>В качестве собственного приложения Windows

Откройте проект `OpcPublisher.sln` в Visual Studio 2017 и создайте решение, нажав клавишу F7.

### <a name="as-docker-container"></a>В качестве контейнера Docker

Для создания приложения в качестве контейнера Docker в Windows используйте файл конфигурации `Dockerfile.Windows`.

Чтобы создать приложение в качестве контейнера Docker в Linux, используйте файл конфигурации `Dockerfile`.

В корне репозитория на компьютере разработки введите в окне консоли следующую команду:

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

Использовать параметр `-f` для команды `docker build` необязательно. По умолчанию используется файл конфигурации `Dockerfile`.

Docker также позволяет выполнять сборку непосредственно из репозитория. Контейнер Docker для Linux можно создать с помощью следующей команды:

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>Настройка узлов OPC UA для публикации

Чтобы настроить, значения каких узлов OPC UA будут публиковаться в Центре Интернета вещей, создайте файл конфигурации в формате JSON. По умолчанию имя этого файла конфигурации — `publishednodes.json`. Приложение обновляет и сохраняет этот файл конфигурации при использовании методов сервера OPC UA **PublishNode** или **UnpublishNode**.

Ниже приведен синтаксис файла конфигурации:

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>Выполнение приложения

### <a name="command-line-options"></a>Параметры командной строки

Чтобы получить полные сведения об использовании приложения, используйте параметр командной строки `--help`. В следующем примере показана структура команды:

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname` — это используемое имя приложения OPC UA. Этот параметр является обязательным. Имя приложения также используется для регистрации издателя в реестре устройств Центра Интернета вещей.

`IoT Hubconnectionstring` — строка подключения владельца Центра Интернета вещей. Это необязательный параметр.

Поддерживаются следующие варианты.

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

Для управления приложением можно использовать следующие переменные среды:
- `_HUB_CS` — задает строку подключения владельца Центра Интернета вещей;
- `_GW_LOGP` — задает имя используемого файла журнала;
- `_TPC_SP` — задает путь для хранения сертификатов доверенных станций;
- `_GW_PNFP` — задает имя файла конфигурации публикации.

Аргументы командной строки отменяют параметры переменных среды.

Как правило, вы можете указать строку подключения владельца Центра Интернета вещей только при первом запуске приложения. Она хранится в хранилище сертификатов платформы в зашифрованном виде.

При последующих вызовах строка подключения считывается из хранилища сертификатов платформы и используется повторно. Если указывать строку подключения при каждом запуске, устройство в реестре устройств Центра Интернета удаляется и создается заново каждый раз.

### <a name="native-on-windows"></a>Собственное приложение в Windows

Чтобы запускать приложение в Windows как собственное, откройте проект `OpcPublisher.sln` в Visual Studio 2017, выполните сборку решения и опубликуйте его. Вы можете запустить приложение в **целевом каталоге**, где оно было опубликовано, с помощью следующей команды:

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>Использование автоматически созданного контейнера

Чтобы запустить приложение в автоматически созданном контейнере, создайте и запустите собственный контейнер:

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>Использование контейнера из hub.docker.com

В DockerHub доступен предварительно созданный контейнер. Чтобы запустить его, выполните следующую команду:

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>Важные моменты при использовании контейнера

#### <a name="access-to-the-publisher-opc-ua-server"></a>Доступ к серверу издателя OPC UA

Сервер OPC UA издателя по умолчанию прослушивает порт 62222. Чтобы настроить этот входной порт в контейнер, необходимо использовать параметр `-p` команды `docker run` :

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>Включение разрешения имен между контейнерами

Чтобы включить разрешение имен из контейнера в другие контейнеры, необходимо выполнить следующее:

- Создайте определяемую пользователем мостовую сеть Docker.
- Подключите контейнер к сети с помощью параметра `--network`.
- Присвойте контейнеру имя с помощью параметра `--name`.

В следующем примере показаны такие параметры конфигурации:

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Теперь к контейнеру могут обратиться другие контейнеры в сети с помощью имени `publisher`.

#### <a name="assign-a-hostname"></a>Присвоение имени узла

Издатель использует имя узла компьютера, на котором он выполняется, для создания сертификата и конечной точки. Docker выбирает случайное имя узла, если вы не задали определенное имя с помощью параметра `-h`. Вот как можно задать для контейнера внутреннее имя узла `publisher`:

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>Использование подключения привязки (общая файловая система)

В некоторых сценариях требуется считать данные о конфигурации с узла или записать на него файлы журнала, вместо использования файловой системы контейнера. Чтобы настроить такое поведение, используйте параметр `-v` команды `docker run` в режиме подключения привязки. Например:

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>Хранилище для сертификатов X509

Хранение сертификатов X509 не работает с подключением привязки, так как владелец должен иметь разрешения на чтение и запись (`rw`) в пути к хранилищу. Вместо этого необходимо использовать параметр `-v` команды `docker run` в режиме тома.

## <a name="debug-the-application"></a>Отладка приложения

### <a name="native-on-windows"></a>Собственное приложение в Windows

Откройте проект `OpcPublisher.sln` в Visual Studio 2017 и начните отладку приложения, нажав клавишу F5.

### <a name="in-a-docker-container"></a>В контейнере Docker

Visual Studio 2017 поддерживает отладку приложений в контейнере Docker с помощью команды `docker-compose`. Однако этот метод не позволяет передавать параметры командной строки.

Как альтернативный вариант отладки, в Visual Studio 2017 поддерживается отладка через `ssh`. Файл конфигурации сборки docker `Dockerfile.ssh`, расположенный в корне репозитория, можно использовать, чтобы создать контейнер с включенным протоколом SSH:

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

Теперь вы можете запустить контейнер для отладки издателя:

```cmd/sh
docker run -it publisherssh
```

В контейнере необходимо запустить управляющую программу **ssh** вручную:

```cmd/sh
service ssh start
```

На этом этапе можно создать сеанс **ssh** в качестве пользователя `root` с паролем `Passw0rd`.

Для подготовки к отладке приложения в контейнере выполните следующие дополнительные действия:

1. На стороне узла создайте файл `launch.json`:

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. Создайте проект и опубликуйте его в каталоге по своему усмотрению.

1. Используйте средство, например `WinSCP`, чтобы скопировать опубликованные файлы в каталог `/root/publisher` в контейнере. Если вы решили использовать другой каталог, обновите свойство `cdw` в файле `launch.json`.

Теперь можно начать отладку, используя следующую команду в окне командной строки Visual Studio:

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>Дальнейшие действия

Далее мы советуем ознакомиться с тем, как [развертывать шлюз в Windows или Linux для предварительно настроенного решения подключенной фабрики](iot-suite-connected-factory-gateway-deployment.md).