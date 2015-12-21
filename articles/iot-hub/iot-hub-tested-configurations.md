<properties
	pageTitle="Совместимость с платформами ОС и оборудованием | Microsoft Azure"
	description="Содержит сводную информацию о совместимости пакетов SDK для устройств IoT с платформами ОС и аппаратным обеспечением."
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
     ms.date="10/09/2015"
     ms.author="hegate"/>

# Совместимость платформ ОС и оборудования с пакетами SDK для устройств

В этом документе описывается совместимость пакетов SDK с различными платформами ОС, а также конкретными конфигурациями устройств, включенными в [программу сертификации для Интернета вещей Microsoft Azure](#microsoft-azure-certified-for-iot). Если у вас уже есть устройство, посмотрите в список устройств, включенных в программу, чтобы найти сведения о совместимости для конкретного устройства. Если вы не уверены, какое устройство используется, просмотрите раздел о совместимости в статье [Платформы ОС и библиотеки](#os-platforms).


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
|Классические приложения Windows| 7, 8, 10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|Другие платформы | Version (версия)|
|:---------------|:------------:|
|mbed | 2,0 |
|TI-RTOS | 2\.x |



## Библиотеки C

[Пакеты SDK для устройств IoT Microsoft Azure для C](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) были протестированы для следующих конфигураций:

|Платформа ОС| Версия|Протоколы|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT |
|mbed OS| 2,0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT |
|Классические приложения Windows| 7, 8, 10 | HTTPS, AMPQ, MQTT |
|Yocto Linux|2,1 | HTTPS, AMQP|



## Библиотеки node.js

[Пакеты SDK для устройств IoT Microsoft Azure для Node.js](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) были протестированы для следующих конфигураций:


|Среда выполнения| Версия|Протоколы|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS|



## Библиотеки Java

[Пакеты SDK для устройств IoT Microsoft Azure для Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) были протестированы для следующих конфигураций:

|Среда выполнения| Версия|Протоколы|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.7 | HTTPS, AMQP |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP|

Пакеты SDK для службы IoT Microsoft Azure для Java были протестированы для следующих конфигураций:

|Среда выполнения| Версия|Протоколы|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP |


## C#

[Пакеты SDK для устройств IoT Microsoft Azure для .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) были протестированы для следующих конфигураций:

|Платформа ОС| Version (версия)|Протоколы|
|:---------|:----------:|:----------:|
|Классические приложения Windows| 7, 8, 10 | HTTPS, AMPQ|
|Windows IoT Core|10 | HTTPS|

Для кода управляемого агента требуется Microsoft .NET Framework версии 4.5.


## Microsoft Azure, сертифицированное для IoT

**Программа сертификации для Интернета вещей Microsoft Azure** — партнерская программа, связывающая более широкую экосистему IoT с Microsoft Azure и позволяющая разработчикам и архитекторам понять сценарии совместимости. В частности, эта программа предоставляет список доверенных сочетаний операционной системы и устройств, который поможет быстро приступить к работе с проектом IoT независимо от того, находится ли проект на стадии тестирования концепции или на пилотном этапе. С сертифицированным сочетанием устройства и операционной системы можно запустить проект IoT быстро, с меньшими усилиями и настройками. Это гарантирует совместимость устройства с набором IoT Azure и центром IoT Azure.

## Устройства, сертифицированные для IoT

Устройства, **сертифицированные для Интернета вещей**, прошли проверку на совместимость с пакетами SDK для IoT Azure и готовы к использованию в приложении IoT. В частности, совместимость определяется на основе платформы операционной системы и языка программирования.

#### Список устройств

Каждое устройство было сертифицировано для работы с нашим пакетом SDK для операционной системы и языка, выбранных производителем устройства. Например, BeagleBone Black работает в Debian с использованием языков C, Javascript и Java. Это означает, что разработчики могут создавать приложения для любого сочетания одного из этих языков программирования и ОС для конкретных устройств.

|Устройство| Протестированные ОС |Язык|
|:---------|:----------|:----------|
|[ADLINK MXE-202i](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1505&seq=&id=&sid=&category=Fanless-Embedded-Computer_Integrated-Embedded-Computer&utm_source=) |Wind River | JavaScript|
|[Ankaa](http://www.e-consystems.com/iMX6-development-board.asp) |Ubuntu | C|
|[Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto) | Javascript, Java|
|[Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto) | Javascript, Java|
|[Arrow DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) |Windows 10 IoT Core | C#|
|[ARTIK](http://developer.samsung.com/artik) |Fedora | C|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | C, Javascript, Java|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | C, Javascript, Java|
|[Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | Javascript, Java|
|[Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2) |Linux Angstrom(Yocto) | Java|
|[Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3) |Windows 10 IoT Core | C#|
|[Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | Javascript, Java|
|[Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) |mbed 2.0 | C|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | C, Javascript|
|[LogicMachine Series](http://openrb.com/products/) |Пользовательский Linux | C|
|[Minnowboard Max](http://www.minnowboard.org/meet-minnowboard-max/) |Windows 7, 8, 10 | C#|
|[NISE 50C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c) |Windows 10 IoT Core | C#|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | C, Javascript, Java |
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Windows 10 IoT Core| C, Javascript, C#|
|[STM32 Nucleo](http://www.st.com/stm32nucleo) |STM32Cube | C|
|[TI CC3200](http://www.ti.com/product/cc3200) |TI-RTOS 2.x | C|

Начните работу с этими устройствами [здесь](https://azure.microsoft.com/develop/iot/get-started/) или посетите наш [репозиторий](https://github.com/Azure/azure-iot-sdks) GitHub и найдите документацию к устройству на нужном вам языке.

## Дальнейшие действия

Дополнительные сведения о разработке решений с использованием устройств, **сертифицированных для Интернета вещей**, см. [здесь](http://azure.com/iotdev).

<!---HONumber=AcomDC_1210_2015-->