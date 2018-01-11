---
title: "Устранение неполадок Azure IoT Edge | Документы Microsoft"
description: "Решения распространенных проблем и получить навыки по устранению неполадок для Azure IoT Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f61f0bf8234e747ae38146d1a5ea030e3163fa3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Общие проблемы и решения для края IoT Azure

Если возникают проблемы Azure IoT Edge в вашей среде, используйте в данной статье в качестве руководства для устранения неполадок и разрешения. 

## <a name="standard-diagnostic-steps"></a>Стандартные действия диагностики 

При возникновении проблем Дополнительные сведения о состоянии устройства IoT Edge, просмотрев журналы контейнера и сообщений, проходящих из устройства. Используйте команды и инструменты в этом разделе для сбора информации. 

* Просмотрите журналы контейнеры docker для выявления проблем. Запустите с вашей развернутой контейнеров, а затем просмотрите контейнеров, которые составляют IoT Edge среды выполнения: агент Edge и Edge концентратора. Журналы агента Edge обычно предоставляют сведения о жизненном цикле каждого контейнера. Журналы Edge концентратора предоставляют сведения о обмена сообщениями и маршрутизации. 

   ```cmd
   docker logs <container name>
   ```

* Просмотр сообщения, отправляемые через концентратор Edge и сбора аналитики при обновлении свойств устройства с подробных журналов из среды выполнения контейнеров.

   ```cmd
   iotedgectl setup --runtime-log-level DEBUG
   ```

* Если возникают проблемы с подключением, проверьте переменные среды edge устройства как строки подключения устройства:

   ```cmd
   docker exec edgeAgent printenv
   ```

Можно также проверить сообщениях, отправляемых между центр IoT и устройствами IoT Edge. Просмотреть эти сообщения с помощью [набора средств Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) расширения для Visual Studio Code. Дополнительные рекомендации см. в разделе [удобный инструмент, при разработке с Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

После изучения журналы и получения сведений, можно попробовать перезапуск среды выполнения Azure IoT Edge.

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Ребро агент останавливает обработку после около минуты

Агент Edge запускает успешного выполнения для около минуты, а затем останавливается. Журналы указывают Edge агент пытается подключиться к центру IOT по AMQP, которое затем около 30 секунд позже попытки подключения с использованием AMQP через websocket. Если это не удается, край агент завершает работу. 

Журналы агента Edge пример:

```
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Основной причиной
Сетевую конфигурацию узла сети препятствует агента Edge получать доступ к сети. Агент пытается сначала подключиться через AMQP (порт 5671). Если это не удается, он пробует websockets (порт 443).

Среда выполнения IoT Edge устанавливает сети для каждого из модулей для взаимодействия на. В Linux эта сеть является сетевой мост. В Windows он использует NAT. Эта проблема чаще на устройствах Windows, с использованием контейнеров Windows, использующие сеть NAT. 

### <a name="resolution"></a>Способы устранения:
Убедитесь, что маршрут к Интернету для IP-адресов, назначенных этой сетевой мост/NAT. Иногда конфигурация VPN на узле переопределяет IoT границы сети. 

## <a name="edge-hub-fails-to-start"></a>Не удается запустить Edge концентратора

Концентратор край не запускается и выводится следующее сообщение в журналы: 

```
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Основной причиной
Занято другим процессом на хост-компьютере выполнило привязку порта 443. Концентратор Edge сопоставляет порты 5671 и порт 443 для использования в сценариях шлюза. Это сопоставление порта, если она уже связан другой процесс этот порт. 

### <a name="resolution"></a>Способы устранения:
Найдите и остановить процесс, который использует порт 443. Этот процесс обычно является веб-сервера.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Edge агента не может иметь доступ к образу модуля (403)
Контейнер не запускается, и журналы агента края Показывать ошибки 403. 

### <a name="root-cause"></a>Основной причиной
Агент край не имеет разрешения на доступ к образу модуля. 

### <a name="resolution"></a>Способы устранения:
Попробуйте выполнить команду `iotedgectl login` еще раз.

## <a name="next-steps"></a>Дальнейшие действия
Вы думаете о обнаружении ошибки в платформе IoT Edge? Пожалуйста [проблемы](https://github.com/Azure/iot-edge/issues) , чтобы мы могли продолжать для улучшения. 
