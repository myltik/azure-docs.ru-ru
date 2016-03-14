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
     ms.date="02/28/2016"
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

[Пакеты SDK для устройств IoT Microsoft Azure для языка C](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) были протестированы для следующих конфигураций:

|Платформа ОС| Версия|Протоколы|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT |
|mbed OS| 2,0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT |
|Классические приложения Windows| 7, 8, 10 | HTTPS, AMQP, MQTT |
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
|Классические приложения Windows| 7, 8, 10 | HTTPS, AMQP|
|Windows IoT Core|10 | HTTPS|

Для кода управляемого агента требуется Microsoft .NET Framework версии 4.5.


## Microsoft Azure, сертифицированное для IoT

**Программа сертификации для Интернета вещей Microsoft Azure** — партнерская программа, связывающая более широкую экосистему IoT с Microsoft Azure и позволяющая разработчикам и архитекторам понять сценарии совместимости. В частности, эта программа предоставляет список доверенных сочетаний операционной системы и устройств, который поможет быстро приступить к работе с проектом IoT независимо от того, находится ли проект на стадии тестирования концепции или на пилотном этапе. С сертифицированным сочетанием устройства и операционной системы можно запустить проект IoT быстро, с меньшими усилиями и настройками. Это гарантирует совместимость устройства с [Azure IoT Suite][lnk-iot-suite] и центром IoT Azure.

## Устройства, сертифицированные для IoT

Устройства, **сертифицированные для Интернета вещей**, прошли проверку на совместимость с пакетами SDK для IoT Azure и готовы к использованию в приложении IoT. В частности, совместимость определяется на основе платформы операционной системы и языка программирования.

#### Список устройств

Каждое устройство было сертифицировано для работы с нашим пакетом SDK для операционной системы и языка, выбранных производителем устройства. Например, BeagleBone Black работает в Debian с использованием языков C, Javascript и Java. Это означает, что разработчики могут создавать приложения для любого сочетания одного из этих языков программирования и ОС для конкретных устройств.

|Устройство| Протестированные ОС |Язык|
|:---------|:----------|:----------|
|[AAEON ACP-1104](http://www.aaeon.com/en/p/infotainment-multi-touch-panel-solutions-1104/) |Windows 10 | C#|
|[AAEON BOXER-6614](http://www.aaeon.com/en/p/fanless-embedded-computers-boxer-6614/) |Windows 10 | C#|
|[AAEON GENE-BT05](http://www.aaeon.com/en/p/3-and-half-inches-subcompact-boards-gene-bt05/) |Windows 10 | C#|
|[AAEON PICO-BT01](http://www.aaeon.com/en/p/pico-itx-boards-pico-bt01) |Windows 10 | C#|
|[AAEON UP](http://www.up-board.org/) |ubilinux | C|
|[AAEON UP](http://www.up-board.org/) |Windows 10 | C#|
|[Acme Systems Arietta G25](http://www.acmesystems.it/arietta) |Debian | C|
|[ADLINK IMB-M43](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1600&seq=&id=&sid=&category=Industrial-Motherboards-&-SBC_ ATX&utm\_source=#) |Windows 10 | C#|
|[ADLINK MXE-200](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1505&seq=&id=&sid=&category=Fanless-Embedded-Computer_Integrated-Embedded-Computer&utm_source=) |Windows 10 | C#|
|[ADLINK MXE-202i](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1589) |Wind River | JavaScript|
|[ADLINK MXE-5400](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1318) |Windows 10 | C#|
|[Advantech Co., ARK-2121L](http://www.advantech.com/products/ark-2000_series_embedded_box_pcs/ark-2121l/mod_dd092808-0832-44bc-b38a-945eb7e016bd) |Windows 10 | C#|
|[Advantech Co., ARK-1123C](http://www.advantech.com/products/92d96fda-cdd3-409d-aae5-2e516c0f1b01/ark-1123c/mod_0b91165c-aa8c-485d-8d25-fde6f88f4873) |Windows 10 | C#|
|[Advantech Co., LTD UNO-1372G](http://www.advantech.com/products/gf-bvl2/uno-1372g/mod_8e63b3c9-b606-4725-a1af-94fccb98bb1a) |Windows 10 | C#|
|[Advantech Co., TREK-674](http://www.advantech.com/products/1-2jsj5t/trek-674/mod_88a737dd-819b-4c8e-8f2e-2bb75b04619b) |Windows 10 | C#|
|[Advantech Co., UTX-3115](http://www.advantech.com/products/bda911fe-28bc-4171-aed3-67f76f6a12c8/utx-3115/mod_fa00d5cd-7d2b-430b-8983-c232bfb9f315) |Windows 10 | C#|
|[Arduino MKR1000](https://www.arduino.cc/en/Main/ArduinoMKR1000) |IDE Arduino | C|
|[Arduino Zero](https://www.arduino.cc/en/Guide/ArduinoZero) |IDE Arduino | C|
|[Arrow DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) |Windows 10 IoT Core | C#|
|[Avalue RIPAC-10P1](http://www.avalue.com.tw/) |Windows 10 | C#|
|[Avalue RITAB-10T1](http://www.avalue.com.tw/product/Intelligent-Systems/Mobile-Solution/Mobile-Solution/RiTab-10T1_2384) |Windows 10 | C#|
|[Axiomtek ICO300](http://www.axiomtek.com/Default.aspx?MenuId=Products&FunctionId=ProductView&ItemId=1151) |Windows 10 | C#|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | C, Javascript, Java|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | C, Javascript, Java|
|[Туннель ComponentSoft RFID](http://www.componentsoft.com/) |Windows 10 | C#|
|[Шлюз Dell Edge Gateway 5000 Series](http://www.dell.com/IoTgateway) |Ubuntu | Java|
|[DFI EC700-BT](http://www.dfi.com.tw/Upload/Product/Documents/BT700.pdf) |Windows 10 | C|
|[e-con Systems Almach](http://www.e-consystems.com/DM3730-development-board.asp) |Linux Yocto | C|
|[e-con Systems Ankaa](http://www.e-consystems.com/iMX6-development-board.asp) |Ubuntu | C|
|[встроенные системы серии LogicMachine](http://openrb.com/products/) |Пользовательский Linux | C|
|[Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) |mbed 2.0 | C|
|[HPE Edgeline EL10](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884747.pdf) |Windows 10 | C#|
|[HPE Edgeline EL20](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884769.pdf) |Windows 10 | C#|
|[IEI ICECARE-10W](http://www.ieimobile.com/index.php?option=com_content&view=article&id=222&Itemid=21) |Windows 10 | C#|
|[IEI DRPC-120](http://www.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0D182494345754583862&id=0E318374091597499543#.VqW3Q_l97Dd) |Windows 10 | C#|
|[IEI IVS-100-BT](http://tw.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0F202412454715193114&id=0F202496627608256517#.VqH1hvl97Dc) |Windows 10 | C#|
|[Ilevia Eve Raspberry](http://www.ilevia.com/overview/) |Debian | C|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | C, Javascript|
|[Inventec Corp Avatar](http://www.inventec.com/indexEN.htm) |Windows 10 IoT Core | C#|
|[Libelium Meshlium Xtreme](http://www.libelium.com/products/meshlium/) |Debian | Java|
|[MechaTracks 3GPI](http://www.mechatrax.com/products/3gpi) |Debian | C|
|[Minnowboard Max](http://www.minnowboard.org/meet-minnowboard-max/) |Windows 7, 8, 10 | C#|
|[MiTAC Computing Technology Pluto E220](http://client.mitac.com/products-Embedded-Box-PC-PlutoE220.html) |Windows 10 | C#|
|[NEXCOM NISE 50C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c) |Windows 10 IoT Core | C#|
|[Pacific Control Systems G2021ES](http://www.pacificcontrols.net/products/G2021ES-Gateway.html) |Ubuntu | C|
|[PANASONIC Toughpad FZ-F1](http://www.panasonic.com/global/home.html) |Windows 10 IoT Mobile Enterprise | C#|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | C, Javascript, Java |
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Windows 10 IoT Core| C, Javascript, C#|
|[Samsung ARTIK](http://developer.samsung.com/artik) |Fedora | C|
|[SOTEC CloudPlug](http://cloudplug.info/) |YOCTO | C|
|[TI CC3200](http://www.ti.com/product/cc3200) |TI-RTOS 2.x | C|
|[Toradex Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto) | Javascript, Java|
|[Toradex Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto) | Javascript, Java|
|[Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | Javascript, Java|
|[Toradex Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2) |Linux Angstrom(Yocto) | Java|
|[Toradex Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3) |Windows 10 IoT Core | C#|
|[Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | Javascript, Java|
|[Trex NGP](http://www.trex.com.tr/en/donanim_dcasngp8739_73.php) |Windows 10 | C#|
|[Trueverit V4](http://www.trueverit.com/) |Пользовательский Linux | C|
|[USISH EDA8909](http://www.usish.com/) |Windows 10 | C#|

[Приступите к работе с этими устройствами](https://azure.microsoft.com/develop/iot/get-started/) или посетите наш [репозиторий](https://github.com/Azure/azure-iot-sdks) GitHub и найдите документацию к устройству на нужном вам языке.

## Дальнейшие действия

Узнайте больше о разработке решений с использованием устройств, [сертифицированных для IoT](http://azure.com/iotdev).


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0302_2016-->