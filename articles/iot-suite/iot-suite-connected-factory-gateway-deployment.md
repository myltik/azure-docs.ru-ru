---
title: "Развертывание шлюза для подключенной фабрики Azure IoT Suite | Документация Майкрософт"
description: "Узнайте, как развернуть шлюз в ОС Windows или Linux, чтобы обеспечить подключение к предварительно настроенному решению подключенной фабрики."
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
ms.date: 07/24/2017
ms.author: dobett
ms.openlocfilehash: b0e6ae705911d7c18643c77b7fe08fdffffa5eb1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>Развертывание шлюза в ОС Windows или Linux для предварительно настроенного решения подключенной фабрики

Программное обеспечение, необходимое для развертывания шлюза для предварительно настроенного решения подключенной фабрики, состоит из двух компонентов:

* *Прокси-сервер OPC* устанавливает подключение к Центру Интернета вещей. Затем *прокси-сервер OPC* ожидает команду и контрольные сообщения от встроенного браузера OPC, работающего на портале решения подключенной фабрики.
* *Издатель OPC* соединяется с существующими локальными серверами OPC UA и пересылает сообщения телеметрии от них в Центр Интернета вещей.

Оба компонента имеют открытый исходный код и доступны в виде файлов с исходным кодом на GitHub и в качестве контейнеров Docker:

| GitHub | DockerHub |
| ------ | --------- |
| [Издатель OPC][lnk-publisher-github] | [Издатель OPC][lnk-publisher-docker] |
| [Прокси-сервер OPC][lnk-proxy-github] | [Прокси-сервер OPC][lnk-proxy-docker] |

Для этих компонентов не требуется общедоступный IP-адрес или заглушка в брандмауэре шлюза. Прокси-сервер OPC и издатель OPC используют только исходящие порты 443, 5671 и 8883.

Описанные в этой статье действия показывают, как развернуть шлюз с помощью Docker в ОС [Windows](#windows-deployment) или [Linux](#linux-deployment). Шлюз обеспечивает подключение к предварительно настроенному решению подключенной фабрики.

> [!NOTE]
> [Edge Интернета вещей Azure] — это программное обеспечение шлюза, выполняемое в контейнере Docker.

## <a name="windows-deployment"></a>Развертывание в ОС Windows

> [!NOTE]
> Если у вас еще нет устройства шлюза, то корпорация Майкрософт рекомендует приобрести коммерческий шлюз у одного из наших партнеров. Посетите [каталог устройств Azure IoT], который содержит список устройств шлюза, совместимых с решением подключенной фабрики. Настройте шлюз, следуя приложенным к устройству инструкциям. Также можно воспользоваться приведенными ниже инструкциями, чтобы вручную настроить один из существующих шлюзов.

### <a name="install-docker"></a>Установка Docker

Установите [Docker для Windows] на устройстве шлюза, работающем под управлением Windows. Во время установки Docker в ОС Windows выберите на хост-компьютере диск, который будет использоваться совместно с Docker. На следующем снимке экрана показано, как в системе Windows совместно используется диск D:

![Установка Docker][img-install-docker]

Затем создайте папку с именем **docker** в корне общего диска.
Можно также выполнить этот шаг после установки Docker из меню **Параметры**.

### <a name="configure-the-gateway"></a>Настройка шлюза

1. Для завершения развертывания шлюза необходима строка подключения **iothubowner** развертывания подключенной фабрики Azure IoT Suite. На [портале Azure] перейдите в Центр Интернета вещей в группе ресурсов, созданной при развертывании решения подключенной фабрики. Щелкните **Политики общего доступа** для доступа к строке подключения **iothubowner**:

    ![Поиск строки подключения Центра Интернета вещей][img-hub-connection]

    Скопируйте значение поля **Строка подключения — первичный ключ**.

1. Настройте шлюз для своего Центра Интернета вещей. Для этого **один раз** запустите из командной строки два модуля шлюза, используя следующую команду:

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** — это имя, которое дается издателю OPC UA в формате **publisher.&lt;полное доменное имя&gt;**. Например, если сеть фабрики имеет название **myfactorynetwork.com**, то **ApplicationName** будет иметь значение **publisher.myfactorynetwork.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** — это строка подключения **iothubowner**, которую вы скопировали на предыдущем шаге. Эта строка подключения используется только на этом шаге и больше не потребуется.

    В дальнейшем для хранения двух сертификатов X.509, используемых модулями шлюза, используется сопоставленная папка D:\\папка_docker (аргумент `-v`).

### <a name="run-the-gateway"></a>Запуск шлюза

1. Перезапустите шлюз с помощью следующих команд:

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. По соображениям безопасности два сертификата X.509, сохраненные в папке D:\\docker, содержат закрытый ключ. Предоставьте доступ к этой папке только учетным данным, которые используются для запуска контейнера Docker (обычно это учетные данные **администратора**). Щелкните правой кнопкой мыши папку D:\\docker, выберите **Свойства** > **Безопасность** > **Изменить**. Предоставьте **администраторам** полный доступ и удалите всех остальных:

    ![Предоставление прав общей папке Docker][img-docker-share]

1. Проверьте сетевое подключение. В окне командной строки введите команду `ping publisher.<your fully qualified domain name>`, чтобы проверить связь со шлюзом. Если точка назначения недоступна, добавьте IP-адрес и имя шлюза в файл hosts на шлюзе. Файл hosts находится в папке **Windows\\System32\\drivers\\etc**.

1. Затем попытайтесь подключиться к издателю, используя локальный клиент OPC UA, запущенный на шлюзе. URL-адрес конечной точки OPC UA — `opc.tcp://publisher.<your fully qualified domain name>:62222`. Если у вас нет клиента OPC UA, скачайте и используйте [клиент OPC UA с открытым кодом].

1. После успешного выполнения этих локальных проверок перейдите на страницу **Connect your own OPC UA Server** (Подключение собственного сервера OPC UA) на портале решения подключенной фабрики. Введите URL-адрес конечной точки издателя (`tcp://publisher.<your fully qualified domain name>:62222`) и нажмите кнопку **Подключить**. После отображения предупреждения о сертификате щелкните **Продолжить**. Затем отобразится сообщение об ошибке, информирующее о том, что издатель не воспринимает веб-клиент UA как доверенный. Чтобы устранить эту ошибку, скопируйте сертификат **UA Web Client** из папки **D:\\docker\\Rejected Certificates\\certs** в папку **D:\\docker\\UA Applications\\certs** на шлюзе. Перезапускать шлюз не требуется. Повторите этот шаг. Теперь вы можете подключиться к шлюзу из облака, а также вы готовы добавить серверы OPC UA в решение.

### <a name="add-your-opc-ua-servers"></a>Добавление серверов OPC UA

1. Перейдите на страницу **Connect your own OPC UA Server** (Подключение собственного сервера OPC UA) на портале решения подключенной фабрики. Выполните те же шаги, что и в предыдущем разделе, чтобы установить отношения доверия между порталом подключенной фабрики и сервером OPC UA. Этот шаг устанавливает взаимное доверие между сертификатами с портала подключенной фабрики и сервера OPC UA и создает подключение.

1. Просмотрите дерево узлов сервера OPC UA, щелкните правой кнопкой мыши узлы OPC и выберите **Опубликовать**. Чтобы публикация работала таким образом, сервер OPC UA и издатель должны находиться в одной сети. Другими словами, если полное доменное имя издателя — **publisher.mydomain.com**, то полное доменное имя сервера OPC UA должно быть, например, **myopcuaserver.mydomain.com**. Если ваши настройки отличаются, можно вручную добавить узлы в файл publishesnodes.json, который находится в папке **D:\\docker**. Файл publishesnodes.json автоматически создается при первой успешной публикации узла OPC.

1. Телеметрия теперь поступает с устройства шлюза. Данные телеметрии можно просмотреть в представлении **Factory Locations** (Расположения фабрик) портала подключенной фабрики в разделе **New Factory** (Новая фабрика).

## <a name="linux-deployment"></a>Развертывание в ОС Linux

> [!NOTE]
> Если у вас еще нет устройства шлюза, то корпорация Майкрософт рекомендует приобрести коммерческий шлюз у одного из наших партнеров. Посетите [каталог устройств Azure IoT], который содержит список устройств шлюза, совместимых с решением подключенной фабрики. Настройте шлюз, следуя приложенным к устройству инструкциям. Также можно воспользоваться приведенными ниже инструкциями, чтобы вручную настроить один из существующих шлюзов.

[Установите Docker] на устройстве шлюза, работающем под управлением Linux.

### <a name="configure-the-gateway"></a>Настройка шлюза

1. Для завершения развертывания шлюза необходима строка подключения **iothubowner** развертывания подключенной фабрики Azure IoT Suite. На [портале Azure] перейдите в Центр Интернета вещей в группе ресурсов, созданной при развертывании решения подключенной фабрики. Щелкните **Политики общего доступа** для доступа к строке подключения **iothubowner**:

    ![Поиск строки подключения Центра Интернета вещей][img-hub-connection]

    Скопируйте значение поля **Строка подключения — первичный ключ**.

1. Настройте шлюз для своего Центра Интернета вещей. Для этого **один раз** запустите из оболочки два модуля шлюза, используя следующую команду:

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** — это имя приложения OPC UA, которое шлюз создает в формате **publisher.&lt;ваше полное доменное имя&gt;**. Например, **publisher.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** — это строка подключения **iothubowner**, которую вы скопировали на предыдущем шаге. Эта строка подключения используется только на этом шаге и больше не потребуется.

    В дальнейшем для хранения двух сертификатов X.509, используемых модулями шлюза, используется папка **/shared** (аргумент `-v`).

### <a name="run-the-gateway"></a>Запуск шлюза

1. Перезапустите шлюз с помощью следующих команд:

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 –-rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. По соображениям безопасности два сертификата X.509, хранящиеся в папке **/shared**, содержат закрытый ключ. Предоставьте доступ к этой папке только учетным данным, которые используются для запуска контейнера Docker. Чтобы задать разрешения только для **корня**, используйте команду оболочки `chmod` для папки.

1. Проверьте сетевое подключение. В оболочке введите команду `ping publisher.<your fully qualified domain name>`, чтобы проверить связь со шлюзом. Если точка назначения недоступна, то добавьте IP-адрес и имя шлюза в файл hosts на шлюзе. Файл hosts находится в папке **/etc**.

1. Затем попытайтесь подключиться к издателю, используя локальный клиент OPC UA, запущенный на шлюзе. URL-адрес конечной точки OPC UA — `opc.tcp://publisher.<your fully qualified domain name>:62222`. Если у вас нет клиента OPC UA, скачайте и используйте [клиент OPC UA с открытым кодом].

1. После успешного выполнения этих локальных проверок перейдите на страницу **Connect your own OPC UA Server** (Подключение собственного сервера OPC UA) на портале решения подключенной фабрики. Введите URL-адрес конечной точки издателя (`tcp://publisher.<your fully qualified domain name>:62222`) и нажмите кнопку **Подключить**. После отображения предупреждения о сертификате щелкните **Продолжить**. Затем отобразится сообщение об ошибке, информирующее о том, что издатель не воспринимает веб-клиент UA как доверенный. Чтобы устранить эту ошибку, скопируйте сертификат **UA Web Client** из папки **/shared/Rejected Certificates/certs** в папку **/shared/UA Applications/certs** на шлюзе. Перезапускать шлюз не требуется. Повторите этот шаг. Теперь вы можете подключиться к шлюзу из облака, а также вы готовы добавить серверы OPC UA в решение.

### <a name="add-your-opc-ua-servers"></a>Добавление серверов OPC UA

1. Перейдите на страницу **Connect your own OPC UA Server** (Подключение собственного сервера OPC UA) на портале решения подключенной фабрики. Выполните те же шаги, что и в предыдущем разделе, чтобы установить отношения доверия между порталом подключенной фабрики и сервером OPC UA. Этот шаг устанавливает взаимное доверие между сертификатами с портала подключенной фабрики и сервера OPC UA и создает подключение.

1. Просмотрите дерево узлов сервера OPC UA, щелкните правой кнопкой мыши узлы OPC и выберите **Опубликовать**. Чтобы публикация работала таким образом, сервер OPC UA и издатель должны находиться в одной сети. Другими словами, если полное доменное имя издателя — **publisher.mydomain.com**, то полное доменное имя сервера OPC UA должно быть, например, **myopcuaserver.mydomain.com**. Если ваши настройки отличаются, можно вручную добавить узлы в файл publishesnodes.json, который находится в папке **/shared**. Файл publishesnodes.json автоматически создается при первой успешной публикации узла OPC.

1. Телеметрия теперь поступает с устройства шлюза. Данные телеметрии можно просмотреть в представлении **Factory Locations** (Расположения фабрик) портала подключенной фабрики в разделе **New Factory** (Новая фабрика).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об архитектуре предварительно настроенного решения подключенной фабрики см. в статье [Общие сведения о наборе Azure IoT Suite][lnk-walkthrough].

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker для Windows]: https://www.docker.com/docker-windows
[каталог устройств Azure IoT]: https://catalog.azureiotsuite.com/?q=opc
[портале Azure]: http://portal.azure.com/
[клиент OPC UA с открытым кодом]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Установите Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Edge Интернета вещей Azure]: https://github.com/Azure/iot-edge (Edge Интернета вещей Azure)

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy