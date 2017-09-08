---
title: "Часто задаваемые вопросы о подключенной фабрике Azure IoT Suite | Документация Майкрософт"
description: "Часто задаваемые вопросы о подключенной фабрике IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: corywink
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 35cf824210a14410d7ea2aedddde0040309901f9
ms.contentlocale: ru-ru
ms.lasthandoff: 08/25/2017

---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>Часто задаваемые вопросы о предварительно настроенном решении для подключенной фабрики IoT Suite

Дополнительные сведения см. в общих [вопросах и ответах](iot-suite-faq.md) об IoT Suite.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>Где можно найти исходный код для предварительно настроенных решений?

Исходный код хранится в следующем репозитории GitHub:

* [Предварительно настроенное решение для подключенной фабрики](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Что такое OPC UA?

Унифицированная архитектура OPC (OPC UA, выпущена в 2008 г.) — это независящий от платформы, сервис-ориентированный стандарт взаимодействия. OPC UA используется в различных производственных системах и устройствах, например в производственных компьютерах, контроллерах ПЛК и датчиках. В OPC UA интегрированы функции спецификаций классической технологии OPC с одной расширяемой платформой со встроенными средствами обеспечения безопасности. Этим стандартом управляет OPC Foundation. [OPC Foundation](http://opcfoundation.org/) — это некоммерческая организация, в которую входит более 440 членов. Ее цель заключается в обеспечении надежного, безопасного взаимодействия между несколькими поставщиками и платформами на основе спецификации OPC через следующие компоненты:

* Инфраструктура
* спецификации;
* Технология
* Процессы

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Почему корпорация Майкрософт выбрала OPC UA для предварительно настроенного решения для подключенной фабрики?

Корпорация Майкрософт выбрала OPC UA, потому что это открытый, незапатентованный и общепризнанный отраслевой стандарт, независящий от платформы. Это требование для решений эталонной архитектуры Industrie 4.0 (RAMI 4.0), чтобы обеспечить взаимодействие между разнообразными производственными процессами и оборудованием. Корпорация Майкрософт знает о том, что сборка решений Industrie 4.0 пользуется спросом у клиентов. Поддержка OPC UA позволяет клиентам избавиться от препятствий на пути к своим целям и обеспечивает непосредственную ценность для бизнеса клиентов.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Как добавить общедоступный IP-адрес к виртуальной машине симуляции?

IP-адрес можно добавить двумя вариантами.

* Используйте сценарий PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` в [этом репозитории](https://github.com/Azure/azure-iot-connected-factory). Передайте имя развертывания в качестве параметра. Для локального развертывания используйте имя `<your username>ConnFactoryLocal`. Этот сценарий выводит IP-адрес виртуальной машины.

* На портале Azure найдите группу ресурсов развертывания. Эта группа имеет имя, указанное вами в качестве имени решения или развертывания (это не относится к локальным развертываниям). Группа ресурсов в локальном развертывании на основе сценария сборки имеет имя `<your username>ConnFactoryLocal`. Теперь добавьте новый ресурс **общедоступного IP-адреса** к группе ресурсов.

> [!NOTE]
> В любом случае убедитесь, что установлены последние обновления, следуя указаниям на [веб-сайте Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Обновляйте версию до тех пор, пока ваша виртуальная машина не станет доступна через общедоступный IP-адрес.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Как удалить общедоступный IP-адрес виртуальной машины симуляции?

IP-адрес можно удалить двумя вариантами.

* Используйте сценарий PowerShell Simulation/Factory/Remove-SimulationPublicIp.ps1 из [этого репозитория](https://github.com/Azure/azure-iot-connected-factory). Передайте имя развертывания в качестве параметра. Для локального развертывания используйте имя `<your username>ConnFactoryLocal`. Этот сценарий выводит IP-адрес виртуальной машины.

* На портале Azure найдите группу ресурсов развертывания. Эта группа имеет имя, указанное вами в качестве имени решения или развертывания (это не относится к локальным развертываниям). Группа ресурсов в локальном развертывании на основе сценария сборки имеет имя `<your username>ConnFactoryLocal`. Теперь удалите ресурс **общедоступного IP-адреса** из группы ресурсов.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Как войти на виртуальную машину симуляции?

Вход на виртуальную машину симуляции поддерживается только при развертывании решения с помощью сценария PowerShell `build.ps1` из [этого репозитория](https://github.com/Azure/azure-iot-connected-factory).

При развертывании решения из www.azureiotsuite.com вы не сможете войти на виртуальную машину, так как пароль формируется случайным образом, и вы не сможете сбросить его.

1. Добавьте общедоступный IP-адрес к виртуальной машине. Дополнительные сведения см. в разделе [Как добавить общедоступный IP-адрес к виртуальной машине симуляции?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Создайте сеанс SSH виртуальной машины с помощью ее IP-адреса.
1. Используйте следующее имя пользователя: `docker`.
1. Используемый пароль зависит от версии, с помощью которой выполнялось развертывание.
    * Для решений, развертываемых с помощью сценария build.ps1 до 1 июня 2017 г., используйте пароль `Passw0rd`.
    * Для решений, развертываемых с помощью сценария build.ps1 после 1 июня 2017 г., пароль можно найти в файле `<name of your deployment>.config.user`. Пароль хранится в параметре **VmAdminPassword**. Пароль формируется случайным образом во время развертывания (если он не указан с помощью параметра `-VmAdminPassword` сценария `build.ps1`).

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Как остановить и запустить все процессы docker на виртуальной машине симуляции?

1. Войдите на виртуальную машину симуляции. Дополнительные сведения см. в разделе [Как войти на виртуальную машину симуляции?](#how-do-i-sign-in-to-the-simulation-vm)
1. Чтобы проверить, какие контейнеры активны, выполните команду `docker ps`.
1. Чтобы остановить все контейнеры симуляции, выполните команду `./stopsimulation`.
1. Чтобы запустить все контейнеры симуляции, сделайте следующее:
    * Экспортируйте переменную оболочки с именем **IOTHUB_CONNECTIONSTRING**. Используйте значение **IotHubOwnerConnectionString** в файле `<name of your deployment>.config.user`. Например:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Запустите `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Как обновить симуляцию на виртуальной машине?

Если вы внесли какие-либо изменения в симуляцию, используйте сценарий PowerShell `build.ps1` в [этом репозитории](https://github.com/Azure/azure-iot-connected-factory). Добавьте команду `updatedimulation`. Этот сценарий создает все компоненты симуляции, останавливает симуляцию на виртуальной машине, а также передает, устанавливает и запускает соответствующие процессы.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Как найти строку подключения Центра Интернета вещей, используемого моим решением?

При развертывании решения с помощью сценария `build.ps1` в [этом репозитории](https://github.com/Azure/azure-iot-connected-factory), строка подключения — это значение **IotHubOwnerConnectionString** в файле `<name of your deployment>.config.user`.

Строку подключения также можно найти на портале Azure. В ресурсе Центра Интернета вещей в группе ресурсов развертывания найдите параметры строки подключения.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Какие устройства Центра Интернета вещей используют симуляцию подключенной фабрики?

Имитация автоматически регистрирует следующие устройства:

* proxy.beijing.corp.contoso;
* proxy.capetown.corp.contoso;
* proxy.mumbai.corp.contoso;
* proxy.munich0.corp.contoso;
* proxy.rio.corp.contoso;
* proxy.seattle.corp.contoso;
* publisher.beijing.corp.contoso;
* publisher.capetown.corp.contoso;
* publisher.mumbai.corp.contoso;
* publisher.munich0.corp.contoso;
* publisher.rio.corp.contoso;
* publisher.seattle.corp.contoso.

Инструмент [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer), или [iothub-explorer](https://github.com/azure/iothub-explorer), позволяет проверить, какие устройства зарегистрированы в Центре Интернета вещей, используемого решением. Чтобы использовать эти инструменты, требуется строка подключения к Центру Интернета вещей в вашем развертывании.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Как получить данные журнала из компонентов симуляции?

Все компоненты симуляции записывают сведения в файлы журнала. Эти файлы можно найти на виртуальной машине в папке `home/docker/Logs`. Чтобы получить журналы, используйте сценарий PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` в [этом репозитории](https://github.com/Azure/azure-iot-connected-factory).

Чтобы выполнить этот сценарий, нужно войти на виртуальную машину. Возможно, вам потребуется предоставить учетные данные для входа. Чтобы найти учетные данные, см. сведения в разделе [Как войти на виртуальную машину симуляции?](#how-do-i-sign-in-to-the-simulation-vm)

Сценарий добавляет общедоступный IP-адрес для виртуальной машины, если он еще отсутствует, или удаляет его. Кроме того, он помещает все файлы журнала в архив и загружает его на рабочую станцию, где ведется разработка.

Вы также можете войти на виртуальную машину по протоколу SSH и проверить файлы журнала в среде выполнения.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Как проверить, отправляет ли симуляция данные в облако?

Инструмент [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) или [iothub-explorer](https://github.com/azure/iothub-explorer) позволяет просмотреть данные, отправленные в Центр Интернета вещей с определенных устройств. Чтобы использовать эти инструменты, требуется знать строку подключения к Центру Интернета вещей в вашем развертывании. Дополнительные сведения см. в разделе [Как найти строку подключения Центра Интернета вещей, используемого моим решением?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Проверьте данные, отправленные одним из устройств издателя:

* publisher.beijing.corp.contoso;
* publisher.capetown.corp.contoso;
* publisher.mumbai.corp.contoso;
* publisher.munich0.corp.contoso;
* publisher.rio.corp.contoso;
* publisher.seattle.corp.contoso.

Если данные отсутствуют в Центре Интернета вещей, существует проблема с симуляцией. Сначала следует проанализировать файлы журнала компонентов симуляции. Дополнительные сведения см. в разделе [Как получить данные журнала из компонентов симуляции?](#how-can-i-get-log-data-from-the-simulation-components) После этого попробуйте остановить и запустить симуляцию, и если данные по-прежнему отсутствуют, обновите симуляцию полностью. Дополнительные сведения см. в разделе [Как обновить симуляцию на виртуальной машине?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="next-steps"></a>Дальнейшие действия

Вы также можете ознакомиться с другими функциями и возможностями предварительно настроенных решений IoT Suite.

* [Обзор предварительно настроенного решения прогнозируемого обслуживания](iot-suite-predictive-overview.md)
* [Начало работы с предварительно настроенным решением для подключенной фабрики](iot-suite-connected-factory-overview.md)
* [Комплексная защита в Интернете вещей](securing-iot-ground-up.md)
