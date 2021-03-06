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
ms.openlocfilehash: d586ca18953b12045fbbaa4a656d78a7192eb88e
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34371241"
---
## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей
Создайте центр Интернета вещей, к которому будет подключено приложение, имитирующее устройство. Ниже показано, как это сделать с помощью портала Azure.

1. Войдите на [портал Azure][lnk-portal].

1. Выберите **Создать ресурс** > **Интернет вещей** > **Центр Интернета вещей**.
   
    ![Навигационная панель портала Azure][1]

1. В области **Центр Интернета вещей** введите следующие сведения для создания Центра Интернета вещей:

   * **Подписка**. Выберите подписку, с помощью которой нужно создать этот Центр Интернета вещей.

   * **Группа ресурсов**. Создайте группу ресурсов для размещения Центра Интернета вещей или используйте имеющуюся. Дополнительные сведения см. в статье об [управлении ресурсами Azure с использованием групп ресурсов][lnk-resource-groups].

   * **Регион**. Выберите ближайшее расположение.

   * **Имя**.Создайте имя Центра Интернета вещей. Если введенное имя доступно, появится зеленая галочка.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Окно с общими сведениями о Центре Интернета вещей][2]

2. Выберите **Next: Size and scale** (Далее: размер и масштаб), чтобы продолжить создание вашего Центра Интернета вещей. 

3. Выберите **ценовую категорию и уровень масштабирования**. Для работы с этой статьей выберите уровень **F1 — "Бесплатный"**, если он доступен для вашей подписки. Дополнительные сведения о ценовых категориях и категориях масштабирования см. в [этой статье][lnk-pricing].

   ![Окно выбора размера и уровня масштабирования Центра Интернета вещей][3]

4. Выберите **Review + create** (Просмотреть и создать).

1. Проверьте сведения о вашем Центре Интернета вещей, а затем щелкните **Создать**. Создание Центра Интернета вещей может занять несколько минут. Ход создания можно отслеживать на панели **уведомлений**.

1. Когда центр Интернета вещей будет создан, щелкните соответствующую плитку на портале Azure и откройте окно свойств. После создания Центра Интернета вещей найдите важные данные для подключения к нему устройств и приложений. Щелкните **Политики общего доступа**.
   
1. В разделе **Политики общего доступа** выберите политику **iothubowner**. Скопируйте значение из поля **Строка подключения — первичный ключ** и сохраните для последующего использования. Дополнительные сведения см. в разделе [Контроль доступа][lnk-access-control] в руководстве разработчика для Центра Интернета вещей Azure.
   
    ![Политики общего доступа][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
