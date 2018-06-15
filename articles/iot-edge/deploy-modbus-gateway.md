---
title: Развертывание Modbus в Azure IoT Edge | Документация Майкрософт
description: Разрешите устройствам, использующим Modbus TCP, взаимодействовать с Центром Интернета, создав устройство шлюза IoT Edge.
author: kgremban
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: kgremban
ms.openlocfilehash: d6e831cdd7cd18305beb617693ee1026bb6429e9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631474"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway---preview"></a>Подключение устройств Modbus TCP через шлюз устройств IoT Edge (предварительная версия)

Чтобы подключить устройства IoT, которые используют протокол Modbus TCP или Modbus RTU, к центру Azure IoT, используйте устройство IoT Edge в качестве шлюза. Устройство шлюза считывает данные с устройства Modbus, а затем передает эти данные в облако с помощью поддерживаемого протокола. 

![Устройства Modbus подключаются к Центру Интернета вещей через шлюз Edge](./media/deploy-modbus-gateway/diagram.png)

В этой статье объясняется, как создать собственный образ контейнера для модуля Modbus (или можно использовать предварительно подготовленный пример), а затем развернуть его на устройство IoT Edge, которое будет выполнять роль шлюза. 

В этой статье предполагается, что вы используете протокол Modbus TCP. Дополнительные сведения о том, как настроить модуль для поддержки Modbus RTU, см. в статье [Развертывание Azure IoT Edge на имитированном устройстве в ОС Windows (предварительная версия)](https://github.com/Azure/iot-edge-modbus) проекта на Github. 

## <a name="prerequisites"></a>предварительным требованиям
* Устройство Azure IoT Edge. Пошаговые инструкции по настройке устройства см. в статье [Развертывание Azure IoT Edge на имитированном устройстве в ОС Windows (предварительная версия)](tutorial-simulate-device-windows.md) или [Развертывание Azure IoT Edge на имитированном устройстве в Linux (предварительная версия)](tutorial-simulate-device-linux.md). 
* Строка подключения первичного ключа для устройства IoT Edge.
* Физическое устройство или имитация устройства, которые поддерживают Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Подготовка контейнера Modbus

Если вы хотите протестировать функции шлюза Modbus, корпорация Майкрософт предлагает использовать пример модуля. Для этого перейдите к разделу [Запуск решения](#run-the-solution) и введите в качестве URI образа следующее: 

```URL
microsoft/azureiotedge-modbus-tcp:1.0-preview
```

Если вы хотите создать собственный модуль и настроить его для своей среды, в проекте Github вы можете найти [модуль Modbus для Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) с открытым кодом. Следуйте инструкциям в этом проекте, чтобы создать образ контейнера. Если вы создаете собственный образ контейнера, см. инструкции по публикации образов контейнеров в реестр и развертывании настраиваемого модуля на устройстве в статье о [разработке и развертывании модуля IoT Edge на C#](tutorial-csharp-module.md). 


## <a name="run-the-solution"></a>Запуск решения
1. Найдите нужный Центр Интернета вещей на [портале Azure](https://portal.azure.com/).
2. Щелкните **IoT Edge (preview)** (IoT Edge (предварительная версия)) и выберите устройство IoT Edge.
3. Щелкните **Set modules** (Настроить модули).
4. Добавьте модуль Modbus:
   1. Выберите **Add IoT Edge modulе** (Добавить модуль IoT Edge).
   2. В поле **Имя** введите modbus.
   3. В поле **Изображение** введите URI образа в примере контейнера: `microsoft/azureiotedge-modbus-tcp:1.0-preview`.
   4. Установите флажок **Включить**, чтобы обновить требуемые свойства двойника модуля.
   5. Скопируйте следующий JSON в текстовое поле. Замените значение **SlaveConnection** IPv4-адресом устройства Modbus.

      ```JSON
      {  
        "properties.desired":{  
          "PublishInterval":"2000",
          "SlaveConfigs":{  
            "Slave01":{  
              "SlaveConnection":"<IPV4 address>",
              "HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{  
                "Op01":{  
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Щелкните **Сохранить**.
5. Вернитесь к окну **Добавление модулей** и нажмите кнопку **Далее**.
7. На шаге **указания маршрутов** скопируйте следующий код JSON в текстовое поле. Этот маршрут отправляет все сообщения, собранные модулем Modbus, в Центр Интернета вещей. В этом маршруте modbusOutput — конечная точка, используемая всеми модулем Modbus для вывода данных, а upstream — специальное место назначения, которое указывает Центру Edge отправлять сообщения в Центр Интернета вещей. 
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Щелкните **Далее**. 
9. В окне **Review Template** (Проверка шаблона) выберите **Отправить**. 
10. Вернитесь на страницу сведений об устройстве и выберите **Обновить**. Вы должны увидеть новый модуль **modbus** который работает вместе со средой выполнения IoT Edge.

## <a name="view-data"></a>Просмотр данных
Просмотр данных, поступающих через модуль modbus:
```cmd/sh
docker logs -f modbus
```

Вы также можете просмотреть данные телеметрии, которые передает устройство, используя [обозреватель Центра Интернета вещей](https://github.com/azure/iothub-explorer). 

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о работе устройств IoT Edge в качестве шлюзов см. в статье [Создание устройства IoT Edge, которое работает как прозрачный шлюз](how-to-create-transparent-gateway.md).
- Дополнительные сведения о работе модулей IoT Edge см. в [обзоре модулей Azure IoT Edge](iot-edge-modules.md).