---
title: Разработка для мобильных устройств с помощью пакетов SDK для Центра Интернета вещей Azure | Документация Майкрософт
description: Руководство для разработчиков. Дополнительные сведения о разработке для мобильных устройств с помощью пакетов SDK для Центра Интернета вещей Azure.
author: yzhong94
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 9c4b4a61698556f14a6362984b04e5cc409b5763
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634953"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Разработка для мобильных устройств с помощью пакетов SDK для Центра Интернета вещей Azure

Объекты в Интернете вещей могут рассматриваться как широкий спектр устройств с различными возможностями: датчики, микроконтроллеры, интеллектуальные устройства, промышленные шлюзы и даже мобильные устройства.  Мобильное устройство может быть устройством Интернета вещей, если оно отправляет данные телеметрии с устройства в облако и управляется облаком.  Оно также может быть устройством, на котором запускается серверное служебное приложение, управляющее другими устройствами Интернета вещей.  В обоих случаях [пакеты SDK Центра Интернета вещей Azure][lnk-sdk-overview] можно использовать для разработки приложений, работающих на мобильных устройствах.  

## <a name="develop-for-native-ios-platform"></a>Разработка для собственной платформы iOS

Пакеты SDK Центра Интернета вещей Azure обеспечивают поддержку собственной платформы iOS с помощью пакета SDK для Центра Интернета вещей Azure для C.  Его можно применять как пакет SDK для iOS, который можно внедрить в проекте Swift или Objective-C XCode.  В iOS пакет SDK для C можно использовать двумя способами.
- Использовать библиотеки CocoaPod в проекте XCode напрямую.  
- Скачать исходный код для пакета SDK для C и создавать приложения для платформы iOS в соответствии с [инструкциями по созданию][lnk-c-devbox] для MacOS.  

Пакет SDK для Центра Интернета вещей Azure для C записывается в C99 для обеспечения максимальной переносимости на различные платформы.  Процесс переноса включает написание тонкого слоя внедрения для компонентов на основе платформ, которые можно найти здесь для [iOS][lnk-ios-pal].  Возможности в пакете SDK для C будут доступны на платформе iOS, включая поддерживаемые примитивы Центра Интернета вещей Azure и функции пакета SDK (например, политика повтора для обеспечения надежности сети).  Интерфейс для пакета SDK iOS также похож на интерфейс для пакета SDK для Центра Интернета вещей Azure для C.  

В этой документации приведены шаги по разработке приложения для устройства или службы приложения на устройстве iOS.
- [Send telemetry from a device to an IoT hub (Swift)][lnk-device-ios-quickstart] (Отправка данных телеметрии с устройства в Центр Интернета вещей (Swift))  
- [Send cloud-to-device messages with IoT Hub (iOS)][lnk-service-ios-quickstart] (Отправка сообщений из облака на устройство с помощью Центра Интернета вещей (iOS))  

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Разработка с библиотеками CocoaPod Центра Интернета вещей Azure

Для разработки iOS пакеты SDK для Центра Интернета вещей Azure выпускают набор библиотек CocoaPod Objective-C.  Чтобы просмотреть последний список библиотек CocoaPod, см. раздел о [CocoaPods для Центра Интернета вещей Microsoft Azure][lnk-cocoapod-list].  После регистрации соответствующих библиотек в проекте XCode имеется два способа для написания кода, связанного с Центром Интернета вещей.
- Функция Objective-C. Если проект написан на языке Objective-C, можно вызывать API непосредственно из пакета SDK для Центра Интернета вещей Azure C.  Если проект написан на языке Swift, вы можете вызвать ``@objc func`` перед созданием функции и перейти к записи логики, относящейся к Центру Интернета вещей Azure, с использованием кода C или Objective-C.  Набор примеров, демонстрирующих, как можно найти эти коды в [репозитории примеров][lnk-ios-samples-repo].  
- Внедрение примеров C. Если вы написали приложение для устройства с помощью кода C, его можно найти непосредственно в проекте XCode.
    - Добавьте файл sample.c в проект XCode из XCode.  
    - Добавьте файл заголовка к зависимости.  Файл заголовка включается в [репозитории примеров][lnk-ios-samples-repo] в качестве примера.  Для получения дополнительной информации об [Objective-C](https://developer.apple.com/documentation/objectivec) посетите страницу документации компании Apple.

## <a name="next-steps"></a>Дополнительная информация
* [Справочник по REST API Центра Интернета вещей][lnk-rest-ref]
* [Исходный код пакета SDK для Интернета вещей Azure для C][lnk-c-sdk]

[lnk-sdk-overview]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks
[lnk-c-devbox]: https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-device-ios-quickstart]:https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-ios
[lnk-service-ios-quickstart]: https://docs.microsoft.com/azure/iot-hub/iot-hub-ios-swift-c2d
[lnk-cocoapod-list]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md
[lnk-ios-samples-repo]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-ios-pal]: https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx
[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
