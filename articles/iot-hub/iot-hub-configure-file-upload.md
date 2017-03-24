---
title: "Настройка отправки файлов с помощью портала Azure | Документация Майкрософт"
description: "Использование портала Azure для настройки Центра Интернета вещей, чтобы включить отправку файлов с подключенных устройств. Содержит сведения о настройке целевой учетной записи хранения Azure."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: aadbee1388e2c2aafaf0a5c50d2af195284d2bec
ms.lasthandoff: 12/07/2016


---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Настройка отправки файлов Центра Интернета вещей с помощью портала Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Передача файла
Чтобы использовать [функции отправки файлов в Центре Интернета вещей][lnk-upload], сначала необходимо связать учетную запись хранения Azure с центром. Выберите параметры **Отправка файла** , чтобы отобразить список свойств передачи файлов для центра IoT, который вы изменяете.

![][13]

**Контейнер хранилища.** На портале Azure выберите контейнер больших двоичных объектов в учетной записи хранения своей текущей подписки Azure, чтобы связать его с центром Интернета вещей. При необходимости можно создать новую учетную запись хранения Azure в колонке **Учетные записи хранения** и новый контейнер больших двоичных объектов в колонке **Контейнеры**. Центр IoT автоматически генерирует универсальные коды ресурсов (URI) подписанных URL-адресов с разрешениями на запись в этом контейнере больших двоичных объектов, чтобы устройства могли их использовать во время передач файлов.

![][14]

**Receive notifications for uploaded files** (Получать уведомления об отправленных файлах). Включите или отключите уведомления об отправке файлов с помощью переключателя.

**SAS TTL** (Срок жизни SAS). Этот параметр определяет срок жизни универсальных кодов ресурса (URI) SAS, возвращаемых центром Интернета вещей на устройство. По умолчанию устанавливается значение "один час", но его можно изменить с помощью ползунка.

**File notification settings default TTL** (Стандартный срок жизни уведомления о файле). Срок жизни уведомления об отправке файла. По умолчанию устанавливается значение "один день", но его можно изменить с помощью ползунка.

**File notification maximum delivery count**(Максимальное число доставок уведомления о файле): число попыток доставки уведомления о передаче файла, предпринимаемых центром IoT. По умолчанию устанавливается значение 10, но его можно изменить с помощью ползунка.

![][15]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о возможностях центра Интернета вещей, касающихся отправки файлов, см. в разделе об [отправке файлов с устройства][lnk-upload] в руководстве разработчика для Центра Интернета вещей.

Дополнительные сведения об управлении центром IoT в Azure см. по следующим ссылкам:

* [Массовое управление удостоверениями устройств Центра Интернета вещей][lnk-bulk]
* [Метрики Центра Интернета вещей][lnk-metrics]
* [Мониторинг операций][lnk-monitor]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]
* [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]
* [Все аспекты безопасности решения Центра Интернета вещей][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md

