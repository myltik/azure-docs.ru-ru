<properties
	pageTitle="Совместимость с платформами ОС | Microsoft Azure"
	description="Содержит сводную информацию о совместимости пакетов SDK для устройств IoT с платформами ОС."
	services="iot-hub"
	documentationCenter=""
	authors="hegate"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/28/2016"
     ms.author="hegate"/>

# Совместимость платформ ОС с пакетами SDK для устройств

В этом документе описывается совместимость пакетов SDK с разными платформами ОС. Если вы не уверены, какое устройство используется, просмотрите раздел о совместимости [Платформа ОС и библиотеки](#os-platforms) в этой статье.

## Программа сертификации Microsoft Azure для IoT

Если у вас уже есть устройство, просмотрите список устройств, включенных в [программу сертификации Microsoft Azure для IoT][lnk-certified], чтобы найти сведения о совместимости для конкретного устройства. Microsoft Azure, сертифицированное для IoT — партнерская программа, связывающая более широкую экосистему IoT с Microsoft Azure и позволяющая разработчикам и архитекторам понять сценарии совместимости. В частности, эта программа предоставляет проверенный список комбинаций операционных систем и устройств, который поможет быстро приступить к работе с проектом IoT независимо от того, находится ли проект на стадии тестирования концепции или на пилотном этапе.

## Платформы ОС

Библиотеки IoT Azure были протестированы на следующих платформах операционных систем:


|Платформы ОС Linux или Unix | Version (версия)|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2,1 |

|Платформы ОС Windows | Version (версия)|
|:---------------|:------------:|
|Классические приложения Windows| 10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|Другие платформы | Version (версия)|
|:---------------|:------------:|
|mbed | 2,0 |
|TI-RTOS | 2\.x |



## Библиотеки C

[Пакеты SDK для устройств IoT Microsoft Azure для языка C](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) были протестированы для следующих конфигураций:

|Платформа ОС| Версия|Протоколы|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT, AMQP через WebSockets |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT, AMQP через WebSockets |
|mbed OS| 2,0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT, AMQP через WebSockets |
|Классические приложения Windows| 10 | HTTPS, AMQP, MQTT, AMQP через WebSockets |
|Yocto Linux|2,1 | HTTPS, AMQP|



## Библиотеки node.js

[Пакеты SDK для устройств IoT Microsoft Azure для Node.js](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) были протестированы для следующих конфигураций:


|Среда выполнения| Версия|Протоколы|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS, AMQP, MQTT, AMQP через WebSockets |



## Библиотеки Java

[Пакеты SDK для устройств IoT Microsoft Azure для Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) были протестированы для следующих конфигураций:

|Среда выполнения| Версия|Протоколы|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.8 | HTTPS, AMQP, MQTT |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP, MQTT|

Пакеты SDK для службы IoT Microsoft Azure для Java были протестированы для следующих конфигураций:

|Среда выполнения| Версия|Протоколы|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP, MQTT |


## C#

[Пакеты SDK для устройств IoT Microsoft Azure для .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) были протестированы для следующих конфигураций:

|Платформа ОС| Version (версия)|Протоколы|
|:---------|:----------:|:----------:|
|Классические приложения Windows| 10 | HTTPS, AMQP, MQTT, AMQP через WebSockets |
|Windows IoT Core|10 | HTTPS |

Для кода управляемого агента требуется Microsoft .NET Framework версии 4.5.


## Дальнейшие действия

- Узнайте больше о программе [сертификации Microsoft Azure для IoT][lnk-certified].
- Узнайте больше о разработке решений с использованием устройств, [сертифицированных для IoT](http://azure.com/iotdev).


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-certified]: iot-hub-certified-devices-linux-c.md

<!---HONumber=AcomDC_0323_2016-->