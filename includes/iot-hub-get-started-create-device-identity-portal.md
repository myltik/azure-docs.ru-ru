---
title: включение файла
description: включение файла
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 1df3e188b71b8fa2d5223bad8bc5914513e26286
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371207"
---
## <a name="create-a-device-identity"></a>Создание удостоверения устройства

В этом разделе объясняется, как создать удостоверение устройства в реестре удостоверений в Центре Интернета вещей с помощью [портала Azure][lnk-azure-portal]. Устройство может подключиться к Центру Интернета вещей, только если в реестре удостоверений есть соответствующая запись. Дополнительные сведения см. в разделе, посвященном реестру удостоверений, в [руководстве разработчика для Центра Интернета вещей Azure][lnk-devguide-identity]. Используйте панель **Устройства IoT** на портале, чтобы создать уникальный идентификатор и ключ устройства, которые будут использоваться им для собственной идентификации в Центре Интернета вещей. Идентификаторы устройств чувствительны к регистру.

1. Войдите на [портал Azure][lnk-azure-portal].

1. Выберите **Все ресурсы** и найдите ресурс Центра Интернета вещей.

1. Когда откроется ресурс Центра Интернета вещей, щелкните панель **Устройства IoT**, а затем выберите **Добавить** в верхней части экрана. 

    ![Создание удостоверения устройства на портале][img-add-device]

1. Укажите имя нового устройства, например **myDeviceId**, и щелкните **Сохранить**. После этого для вашего Центра Интернета вещей будет создано удостоверение устройства.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Добавление нового устройства][img-create-device]

1. В списке устройств щелкните недавно созданное устройство и скопируйте значение в поле **Строка подключения — первичный ключ** для последующего использования.

    ![Строка подключения к устройству.][img-connection-string]

> [!NOTE]
> В реестре удостоверений в Центре Интернета вещей хранятся только идентификаторы устройств, необходимые для безопасного доступа к Центру Интернета вещей. В этом реестре хранятся идентификаторы и ключи устройств, которые используются в качестве учетных данных безопасности, и флажок включения или выключения, который позволяет вам отключить доступ для отдельного устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. Дополнительные сведения см. в [руководстве разработчика для Центра Интернета вещей][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-add-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png
[img-create-device]:./media/iot-hub-get-started-create-device-identity-portal/add-device.png

<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

