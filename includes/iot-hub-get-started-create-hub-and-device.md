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
ms.openlocfilehash: f8cd78e63099f864c5fc54b6268f6e558d738626
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371377"
---
## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

После создания Центра Интернета вещей найдите важные данные для подключения к нему устройств и приложений. 

В меню навигации Центра Интернета вещей откройте **политики общего доступа**.
Выберите политику **iothubowner** и скопируйте значение из поля **Строка подключения — первичный ключ** Центра Интернета вещей. Дополнительные сведения см. в статье [Управление доступом к Центру Интернета вещей](../articles/iot-hub/iot-hub-devguide-security.md).

   > [!NOTE] 
   > Строка подключения iothubowner в этом руководстве по установке не понадобится. Однако она может понадобиться для некоторых руководств по различным сценариям Интернета вещей после завершения этой установки.

   ![Получение строки подключения Центра Интернета вещей](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Регистрация устройства в Центре Интернета вещей ваших устройств

1. В меню навигации Центра Интернета вещей выберите **Устройства IoT**, а затем щелкните **Добавить**, чтобы зарегистрировать устройство в Центре Интернета вещей.

   ![Добавление устройства в области "Устройства IoT" Центра Интернета вещей](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Введите **идентификатор нового устройства**. Идентификаторы устройств чувствительны к регистру.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Выберите команду **Сохранить**.
5. Создав устройство, откройте его из списка области **Устройства IoT**.
6. Скопируйте значение из поля **Строка подключения — первичный ключ** для последующего использования.

   ![Получение строки подключения устройства](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
