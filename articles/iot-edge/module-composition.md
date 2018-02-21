---
title: "Составление модулей Azure IoT Edge | Документация Майкрософт"
description: "Узнайте, что входит в модули Azure IoT Edge и как они могут быть повторно использованы."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f3bc2f14b182e502c651ff44ef49b88cd34e1f50
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)

Azure IoT Edge позволяет составить несколько модулей IoT Edge перед их развертыванием на устройства IoT Edge. В этой статье описаны наиболее важные аспекты составления нескольких модулей IoT Edge перед развертыванием. 

## <a name="the-deployment-manifest"></a>Манифест развертывания
*Манифест развертывания* — это документ JSON, который описывает:

* какие модули IoT Edge должны развертываться, а также параметры создания и управления ими;
* конфигурацию концентратора Edge, которая описывает поток сообщений между модулями, а также между модулями и Центром Интернета вещей;
* при необходимости значения для установки требуемых свойств двойников модулей, чтобы настроить приложения отдельного модуля.

В руководствах по Azure IoT Edge манифест развертывания создается с помощью мастера на портале Azure IoT Edge. Также с помощью REST или пакета SDK службы Центра Интернета вещей можно применить манифест развертывания программно. Дополнительные сведения о развертывании IoT Edge см. в статье [Understand IoT Edge deployments for single devices or at scale - preview][lnk-deploy] (Основные сведения о развертываниях IoT Edge для одного устройства или в требуемом масштабе (предварительная версия)).

На высоком уровне манифест развертывания настраивает требуемые свойства модулей IoT Edge, развернутых на устройстве IoT Edge. Два из этих модулей всегда присутствуют: агент Edge и концентратор Edge.

Манифест имеет следующую структуру:

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
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

Пример манифеста развертывания предоставляется в конце этого раздела.

> [!IMPORTANT]
> Все устройства IoT Edge должны быть настроены с помощью манифеста развертывания. Недавно установленная среда выполнения IoT Edge будет сообщать код ошибки, пока не будет настроена с помощью допустимого манифеста. 

### <a name="specify-the-modules"></a>Указание модулей
Требуемые свойства двойника модуля агента Edge содержат: нужные модули, варианты конфигурации и управления ими, а также параметры настройки для агента Edge.

На высоком уровне этот раздел манифеста содержит ссылки на образы модулей и варианты управления для модулей среды выполнения IoT Edge (агент Edge и концентратор Edge), а также пользовательские модули.

Дополнительные сведения об этом разделе манифеста см. в разделе [Требуемые свойства двойника агента Edge][lnk-edgeagent-desired].

> [!NOTE]
> Допустимым является манифест развертывания, который содержит только среду выполнения IoT Edge (агент и концентратор).


### <a name="specify-the-routes"></a>Указание маршрутов
Концентратор Edge предоставляет способ декларативно маршрутизировать сообщения между модулями, а также между модулями и Центром Интернета вещей.

Маршруты имеют следующий синтаксис:

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

*Источник* может быть любым из следующих:

| Источник | ОПИСАНИЕ |
| ------ | ----------- |
| `/*` | Все сообщения с устройства в облако из любого устройства или модуля. |
| `/messages/*` | Все сообщения с устройства в облако, отправленные устройством или модулем с некоторыми выходными данными или без них. |
| `/messages/modules/*` | Все сообщения с устройства в облако, отправленные модулем с некоторыми выходными данными или без них. |
| `/messages/modules/{moduleId}/*` | Все сообщения с устройства в облако, отправленные {ИД_модуля} без выходных данных. |
| `/messages/modules/{moduleId}/outputs/*` | Все сообщения с устройства в облако, отправленные {ИД_модуля} с некоторыми выходными данными. |
| `/messages/modules/{moduleId}/outputs/{output}` | Все сообщения с устройства в облако, отправленные {ИД_модуля} с использованием {выходные данные}. |

Условие может быть любым условием, поддерживаемым [языком запросов Центра Интернета вещей][lnk-iothub-query] для правил маршрутизации Центра Интернета вещей.

Приемник может принять одно из следующих значений:

| Приемник | ОПИСАНИЕ |
| ---- | ----------- |
| `$upstream` | Отправляет сообщение в Центр Интернета вещей. |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Отправляет сообщение во входные данные `{input}` модуля `{moduleId}`. |

Следует отметить, что концентратор Edge предоставляет однократные гарантии, то есть сообщения будут храниться локально в случае, если маршрут не сможет доставить сообщение в свой приемник, например, концентратор Edge не может подключиться к Центру Интернета вещей или целевой модуль не подключен.

Концентратор Edge сохраняет сообщения до времени, указанного в свойстве `storeAndForwardConfiguration.timeToLiveSecs` требуемых свойств концентратора Edge.

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>Указание требуемых свойств двойника модуля

В манифесте развертывания можно указать требуемые свойства двойника модуля каждого пользовательского модуля, указанного в разделе агента Edge.

Если требуемые свойства заданы в манифесте развертывания, они перезаписывают любые требуемые свойства, находящиеся на данный момент в двойнике модуля.

Если не указать требуемые свойства двойника модуля в манифесте развертывания, Центр Интернета вещей не будет никоим образом изменять двойник модуля. Вы сможете задать требуемые свойства программно.

### <a name="deployment-manifest-example"></a>Пример манифеста развертывания

Этот пример документа JSON манифеста развертывания.

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

## <a name="reference-edge-agent-module-twin"></a>Ссылка: двойник модуля агента Edge

Двойник модуля для агента Edge называется `$edgeAgent`. Он координирует взаимодействие между агентом Edge, выполняющимся на устройстве, и Центром Интернета вещей.
Требуемые свойства задаются при применении манифеста развертывания на конкретном устройстве в ходе развертывания на одно устройство или несколько. Дополнительные сведения о развертывании модулей на устройствах IoT Edge см. в статье [Understand IoT Edge deployments for single devices or at scale - preview][lnk-deploy] (Основные сведения о развертываниях IoT Edge для одного устройства или в требуемом масштабе (предварительная версия)).

### <a name="edge-agent-twin-desired-properties"></a>Требуемые свойства двойника агента Edge

| Свойство | ОПИСАНИЕ | Обязательно |
| -------- | ----------- | -------- |
| schemaVersion | Должно быть "1.0". | Yes |
| runtime.type | Должно быть "docker". | Yes |
| runtime.settings.minDockerVersion | Задайте минимальную версию Docker, которая требуется для этого манифеста развертывания. | Yes |
| runtime.settings.loggingOptions | Переведенные в строку JSON, содержащую параметры ведения журнала для контейнера агента Edge. [Параметры ведения журнала Docker][lnk-docker-logging-options] | Нет  |
| systemModules.edgeAgent.type | Должно быть "docker". | Yes |
| systemModules.edgeAgent.settings.image | Универсальный код ресурса (URI) образа агента Edge. В настоящее время агент Edge не может обновить себя сам. | Yes |
| systemModules.edgeAgent.settings.createOptions | Переведенные в строку JSON, содержащую параметры для создания контейнера агента Edge. [Параметры создания Docker][lnk-docker-create-options] | Нет  |
| systemModules.edgeAgent.configuration.id | Идентификатор развертывания, которое развернуло этот модуль. | Этот параметр задается в Центре Интернета вещей при применении этого манифеста с помощью развертывания. Не является частью манифеста развертывания. |
| systemModules.edgeHub.type | Должно быть "docker". | Yes |
| systemModules.edgeHub.status | Должно быть "running". | Yes |
| systemModules.edgeHub.restartPolicy | Должно быть "always". | Yes |
| systemModules.edgeHub.settings.image | Универсальный код ресурса (URI) образа концентратора Edge. | Yes |
| systemModules.edgeHub.settings.createOptions | Переведенные в строку JSON, содержащую параметры для создания контейнера концентратора Edge. [Параметры создания Docker][lnk-docker-create-options] | Нет  |
| systemModules.edgeHub.configuration.id | Идентификатор развертывания, которое развернуло этот модуль. | Этот параметр задается в Центре Интернета вещей при применении этого манифеста с помощью развертывания. Не является частью манифеста развертывания. |
| modules.{ИД_модуля}.version | Определяемая пользователем строка, представляющая версию этого модуля. | Yes |
| modules.{ИД_модуля}.type | Должно быть "docker". | Yes |
| modules.{ИД_модуля}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | Yes |
| modules.{ИД_модуля}.settings.image | Универсальный код ресурса (URI) для образа модуля. | Yes |
| modules.{ИД_модуля}.settings.createOptions | Переведенные в строку JSON, содержащую параметры для создания контейнера модуля. [Параметры создания Docker][lnk-docker-create-options] | Нет  |
| modules.{ИД_модуля}.configuration.id | Идентификатор развертывания, которое развернуло этот модуль. | Этот параметр задается в Центре Интернета вещей при применении этого манифеста с помощью развертывания. Не является частью манифеста развертывания. |

### <a name="edge-agent-twin-reported-properties"></a>Сообщаемые свойства двойника агента Edge

Сообщаемые свойства агента Edge включают в себя три основных элемента информации:

1. состояние применения последних требуемых свойств;
2. состояние модулей, запущенных на устройстве, зарегистрированное агентом Edge;
3. копия требуемых свойств, выполняемых в данное время на устройстве.

Последний элемент данных полезен в случае, если требуемые свойства не были успешно применены в среде выполнения и устройство по прежнему работает под управлением предыдущего манифеста развертывания.

> [!NOTE]
> Сообщаемые свойства агента Edge полезны, так как к ним можно выполнять запросы с помощью [языка запросов Центра Интернета вещей][lnk-iothub-query], чтобы узнать состояние развертывания в масштабе. Дополнительные сведения о том, как использовать эту функцию см. в статье [Understand IoT Edge deployments for single devices or at scale - preview][lnk-deploy] (Основные сведения о развертываниях IoT Edge для одного устройства или в требуемом масштабе (предварительная версия)).

Следующая таблица не включает сведения, которые копируются из требуемых свойств.

| Свойство | ОПИСАНИЕ |
| -------- | ----------- |
| lastDesiredVersion | Это свойство относится к последней версии требуемых свойств, обрабатываемых агентом Edge. |
| lastDesiredStatus.code | Это код состояния, ссылающийся на последние требуемые свойства, используемые агентом Edge. Допустимые значения: `200` — успех, `400` — недопустимая конфигурация,`412` — недопустимая версия схемы, `417` — нужные свойства пусты, `500` — сбой. |
| lastDesiredStatus.description | Текстовое описание состояния. |
| deviceHealth | Имеет значение `healthy`, если состояние среды выполнения всех модулей имеет значение `running` или `stopped`, в противном случае — `unhealthy`. |
| configurationHealth.{ИД_развертывания}.health | Имеет значение `healthy`, если состояние среды выполнения всех модулей, установленное развертыванием {ИД_развертывания}, имеет значение `running` или `stopped`, в противном случае — `unhealthy`. |
| runtime.platform.OS | Сообщает ОС, выполняющуюся на устройстве. |
| runtime.platform.architecture | Сообщает архитектуру ЦП на устройстве. |
| systemModules.edgeAgent.runtimeStatus | Состояние агента Edge : {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Текстовое описание сообщаемого состояния агента Edge. |
| systemModules.edgeHub.runtimeStatus | Текущее состояние концентратора Edge: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Текстовое описание текущего состояния концентратора Edge, если он неработоспособен. |
| systemModules.edgeHub.exitCode | Если выполнен выход, код завершения, сообщаемый контейнером концентратора Edge. |
| systemModules.edgeHub.startTimeUtc | Время последнего запуска концентратора Edge. |
| systemModules.edgeHub.lastExitTimeUtc | Время последнего завершения работы концентратора Edge. |
| systemModules.edgeHub.lastRestartTimeUtc | Время последнего перезапуска концентратора Edge. |
| systemModules.edgeHub.restartCount | Количество раз, когда этот модуль был перезагружен в рамках политики перезапуска. |
| modules.{ИД_модуля}.runtimeStatus | Текущее состояние модуля: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{ИД_модуля}.statusDescription | Текстовое описание текущего состояния модуля, если он неработоспособен. |
| modules.{ИД_модуля}.exitCode | Если выполнен выход, код завершения, сообщаемый контейнером модуля. |
| modules.{ИД_модуля}.startTimeUtc | Время последнего запуска модуля. |
| modules.{ИД_модуля}.lastExitTimeUtc | Время последнего завершения работы модуля. |
| modules.{ИД_модуля}.lastRestartTimeUtc | Время последнего перезапуска модуля. |
| modules.{ИД_модуля}.restartCount | Количество раз, когда этот модуль был перезагружен в рамках политики перезапуска. |

## <a name="reference-edge-hub-module-twin"></a>Ссылка: двойник модуля концентратора Edge

Двойник модуля концентратора Edge называется `$edgeHub`. Он координирует взаимодействие между концентратором Edge, выполняющимся на устройстве, и Центром Интернета вещей.
Требуемые свойства задаются при применении манифеста развертывания на конкретном устройстве в ходе развертывания на одно устройство или несколько. Дополнительные сведения о развертывании модулей на устройствах IoT Edge см. в статье [Understand IoT Edge deployments for single devices or at scale - preview][lnk-deploy] (Основные сведения о развертываниях IoT Edge для одного устройства или в требуемом масштабе (предварительная версия)).

### <a name="edge-hub-twin-desired-properties"></a>Требуемые свойства двойника концентратора Edge

| Свойство | ОПИСАНИЕ | Требуется в манифесте развертывания |
| -------- | ----------- | -------- |
| schemaVersion | Должно быть "1.0". | Yes |
| routes.{имя_маршрута} | Строка, представляющая маршрут концентратора Edge. | Элемент `routes` может присутствовать, но быть пустым. |
| storeAndForwardConfiguration.timeToLiveSecs | Время в секундах, в течение которого концентратор Edge сохраняет сообщения в случае отключения конечных точек маршрутизации, например отключения от Центра Интернета вещей или локального модуля. | Yes |

### <a name="edge-hub-twin-reported-properties"></a>Сообщаемые свойства двойника концентратора Edge

| Свойство | ОПИСАНИЕ |
| -------- | ----------- |
| lastDesiredVersion | Это свойство относится к последней версии требуемых свойств, обрабатываемых концентратором Edge. |
| lastDesiredStatus.code | Это код состояния, ссылающийся на последние требуемые свойства, используемые концентратором Edge. Допустимые значения: `200` — успех, `400` — недопустимая конфигурация, `500` — сбой. |
| lastDesiredStatus.description | Текстовое описание состояния. |
| clients.{удостоверение устройства или модуля}.status | Состояние подключения этого устройства или модуля. Возможные значения {"connected" \| "disconnected"}. В отключенном состоянии могут находиться только удостоверения модуля. Подчиненные устройства, подключающиеся к концентратору Edge, отображаются только при подключении. |
| clients.{удостоверение устройства или модуля}.lastConnectTime | Время последнего подключения модуля или устройства. |
| clients.{удостоверение устройства или модуля}.lastDisconnectTime | Время последнего отключения модуля или устройства. |

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы знаете, как используются модули IoT Edge, ознакомьтесь со статьей [Understand the requirements and tools for developing IoT Edge modules - preview][lnk-module-dev] (Общие сведения о требованиях и средствах разработки модулей IoT Edge (предварительная версия)).

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
