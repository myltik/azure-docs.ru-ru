---
title: Составление модулей Azure IoT Edge | Документация Майкрософт
description: Узнайте, что входит в модули Azure IoT Edge и как они могут быть повторно использованы.
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 7df566ced755e1e817b3107dac8f17e9f6e9b8e4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)

На каждом устройстве IoT Edge работает по меньшей мере два модуля – $edgeAgent и $edgeHub, составляющие среду выполнения IoT Edge. Помимо этих двух стандартных модулей на любом устройстве IoT Edge можно запустить множество других модулей для выполнения любого числа процессов. При одновременном развертывании этих модулей на устройстве необходим способ объявления о том, какие модули включены и каким образом они взаимодействуют друг с другом. 

*Манифест развертывания* — это документ JSON, который описывает:

* какие модули IoT Edge должны развертываться, а также параметры создания и управления ими;
* конфигурацию концентратора Edge, которая описывает поток сообщений между модулями и в конечном итоге к Центру Интернета вещей;
* при необходимости значения для установки требуемых свойств двойников модулей, чтобы настроить приложения отдельного модуля.

Все устройства IoT Edge должны быть настроены с помощью манифеста развертывания. Недавно установленная среда выполнения IoT Edge будет сообщать код ошибки, пока не будет настроена с помощью допустимого манифеста. 

В руководствах по Azure IoT Edge манифест развертывания создается с помощью мастера на портале Azure IoT Edge. Также с помощью REST или пакета SDK службы Центра Интернета вещей можно применить манифест развертывания программно. Дополнительные сведения о развертывании IoT Edge см. в статье [Understand IoT Edge deployments for single devices or at scale - preview][lnk-deploy] (Основные сведения о развертываниях IoT Edge для одного устройства или в требуемом масштабе (предварительная версия)).

## <a name="create-a-deployment-manifest"></a>Создание манифеста развертывания

На высоком уровне манифест развертывания настраивает требуемые свойства двойника модуля для развернутых модулей IoT Edge на устройстве IoT Edge. Два из этих модулей всегда присутствуют: агент Edge и концентратор Edge.

Допустимым является только тот манифест развертывания, который содержит среду выполнения IoT Edge (агент и концентратор).

Манифест имеет следующую структуру:

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Настройка модулей

Помимо установки требуемых свойств развертываемых модулей в среде выполнения IoT Edge необходимо указать способ установки этих модулей. Сведения по управлению и настройке всех модулей добавляются в требуемые свойства **$edgeAgent**. Сюда также входят параметры настройки самого агента Edge. 

Полный список свойств, которые могут или должны быть включены, см. в статье [Свойства двойников модулей EdgeAgent и EdgeHub](module-edgeagent-edgehub.md).

Свойства $edgeAgent имеют такую структуру:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "{module1}": { //optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Объявление маршрутов

Концентратор Edge предоставляет способ декларативно маршрутизировать сообщения между модулями, а также между модулями и Центром Интернета вещей. Концентратор Edge управляет всем обменом данными, так что информация о маршруте содержится в требуемых свойствах **$edgeHub**. Один экземпляр развертывания может иметь несколько маршрутов.

Маршруты объявляются в требуемых свойствах **$edgeHub**. Синтаксис объявления следующий:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Каждый маршрут имеет исходную и конечную точки. При необходимости можно также добавить условие, чтобы фильтровать сообщения. 


### <a name="source"></a>Источник
Исходная точка определяет то место, откуда поступают сообщения. В качестве такой точки может выступать любой из следующих вариантов:

| Источник | ОПИСАНИЕ |
| ------ | ----------- |
| `/*` | Все сообщения с устройства в облако из любого устройства или модуля. |
| `/messages/*` | Все сообщения с устройства в облако, отправленные устройством или модулем с некоторыми выходными данными или без них. |
| `/messages/modules/*` | Все сообщения с устройства в облако, отправленные модулем с некоторыми выходными данными или без них. |
| `/messages/modules/{moduleId}/*` | Все сообщения с устройства в облако, отправленные {ИД_модуля} без выходных данных. |
| `/messages/modules/{moduleId}/outputs/*` | Все сообщения с устройства в облако, отправленные {ИД_модуля} с некоторыми выходными данными. |
| `/messages/modules/{moduleId}/outputs/{output}` | Все сообщения с устройства в облако, отправленные {ИД_модуля} с использованием {выходные данные}. |

### <a name="condition"></a>Условие
Условие является необязательным при объявлении маршрута. Если нужно передать все сообщения от конечной точки к исходной, просто не заполняйте предложение **WHERE**. Можно также воспользоваться [языком запросов Центра Интернета вещей][lnk-iothub-query] для фильтрации определенных сообщений или типов сообщений, удовлетворяющих заданному условию.

Сообщения, которые проходят между модулями в IoT Edge, форматируются так же, как сообщения, которые проходят между вашими устройствами и Центром Интернета вещей. Все сообщения представлены в формате JSON с параметрами **systemProperties**, **appProperties** и **body**. 

Запросы для всех трех параметров создаются с помощью следующего синтаксиса: 

* свойства системы — `$<propertyName>` или `{$<propertyName>}`;
* свойства приложения — `<propertyName>`;
* свойства текста —`$body.<propertyName>`. 

Примеры создания запросов к свойствам сообщений см. в разделе [Выражения запросов по маршрутам сообщений, отправляемых с устройства в облако](../iot-hub/iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).

Характерным примером для IoT Edge является ситуация, когда вам нужно выполнить фильтрацию для сообщений, поступивших на устройство шлюза с конечного устройства. Поступающие от модулей сообщения содержат системное свойство **connectionModuleId**. Так что, если нужно направить сообщения от конечных устройств к Центру Интернета вещей, используйте следующий маршрут, чтобы исключить сообщения от модулей.

```sql
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Приемник
Конечная точка определяет пункт назначения отправленных сообщений. В качестве такой точки может выступать любой из следующих вариантов:

| Приемник | ОПИСАНИЕ |
| ---- | ----------- |
| `$upstream` | Отправляет сообщение в Центр Интернета вещей. |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Отправляет сообщение во входные данные `{input}` модуля `{moduleId}`. |

Следует отметить, что концентратор Edge предоставляет по крайней мере однократные гарантии того, что сообщения будут сохранены локально в случае невозможности доставки по указанному маршруту в пункт назначения, так, например, концентратор Edge не может подключиться к Центру Интернета вещей или требуемый модуль недоступен.

Концентратор Edge хранит сообщения до окончания срока хранения, указанного в свойстве `storeAndForwardConfiguration.timeToLiveSecs` [требуемых свойств концентратора Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Определение или обновление требуемых свойств 

В манифесте развертывания можно указать требуемые свойства для двойника каждого модуля, развернутого на устройстве IoT Edge. Если требуемые свойства заданы в манифесте развертывания, они перезаписывают любые требуемые свойства, находящиеся на данный момент в двойнике модуля.

Если не указать требуемые свойства двойника модуля в манифесте развертывания, Центр Интернета вещей не будет никоим образом изменять двойник модуля. Вы сможете задать требуемые свойства программно.

Для изменения двойников модуля используются те же механизмы, что и для двойников устройства. Дополнительные сведения см. в руководстве [Общие сведения о двойниках устройств и их использование в Центре Интернета вещей](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins).   

## <a name="deployment-manifest-example"></a>Пример манифеста развертывания

Этот пример документа JSON манифеста развертывания.

```json
{
"moduleContent": {
    "$edgeAgent": {
        "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
                "type": "docker",
                "settings": {
                    "minDockerVersion": "v1.25",
                    "loggingOptions": ""
                }
            },
            "systemModules": {
                "edgeAgent": {
                    "type": "docker",
                    "settings": {
                    "image": "microsoft/azureiotedge-agent:1.0-preview",
                    "createOptions": ""
                    }
                },
                "edgeHub": {
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-hub:1.0-preview",
                    "createOptions": ""
                    }
                }
            },
            "modules": {
                "tempSensor": {
                    "version": "1.0",
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                    "createOptions": "{}"
                    }
                },
                "filtermodule": {
                    "version": "1.0",
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "myacr.azurecr.io/filtermodule:latest",
                    "createOptions": "{}"
                    }
                }
            }
        }
    },
    "$edgeHub": {
        "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
                "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
            },
            "storeAndForwardConfiguration": {
                "timeToLiveSecs": 10
            }
        }
    }
}
}
```

## <a name="next-steps"></a>Дополнительная информация

* Полный список свойств, которые могут или должны быть у $edgeAgent и $edgeHub, см. в статье [Свойства двойников модулей EdgeAgent и EdgeHub](module-edgeagent-edgehub.md).

* Теперь, когда вы знаете, как используются модули IoT Edge, ознакомьтесь со статьей [Understand the requirements and tools for developing IoT Edge modules - preview][lnk-module-dev] (Общие сведения о требованиях и средствах разработки модулей IoT Edge (предварительная версия)).

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
