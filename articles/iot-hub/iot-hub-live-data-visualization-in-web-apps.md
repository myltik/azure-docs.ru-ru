---
title: "Визуализация данных, поступающих от датчиков в реальном времени, из Центра Интернета вещей с помощью веб-приложений | Документация Майкрософт"
description: "Сведения о визуализации данных о температуре и влажности, собранных с датчиков и переданных в Центр Интернета вещей Azure, с помощью веб-приложений Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "визуализация данных, полученных в реальном времени, визуализация интерактивных данных, визуализация данных датчиков"
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: f3bb01da7764e467963a47d3d5485679411c9167
ms.lasthandoff: 04/25/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-azure-web-apps"></a>Визуализация данных, поступающих от датчиков в реальном времени, из Центра Интернета вещей с помощью веб-приложений Azure

![Сквозная схема](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Что вы узнаете

Из этого урока вы узнаете, как визуализировать данные датчиков, полученные в реальном времени и отправленные в Центр Интернета вещей, с помощью веб-приложения, размещенного в Azure. Если вы хотите визуализировать данные в Центре Интернета вещей с помощью Power BI, см. сведения в статье [Visualize real-time sensor data from Azure IoT Hub using Power BI](iot-hub-live-data-visualization-in-power-bi.md) (Использование Power BI для визуализации данных, получаемых Центром Интернета вещей от датчика в режиме реального времени).

## <a name="what-you-do"></a>В рамках этого руководства мы:

- создадим веб-приложение на портале Azure;
- добавим группу потребителей, чтобы обеспечить доступ к данным в Центре Интернета вещей;
- настроим веб-приложение для чтения данных датчиков из Центра Интернета вещей;
- отправим созданное веб-приложение для размещения в Azure;
- откроем веб-приложение, чтобы просмотреть данные о температуре и влажности, полученные в реальном времени, из Центра Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы

- Изучите руководство [Настройка вашего устройства](iot-hub-raspberry-pi-kit-node-get-started.md), где описаны следующие требования:
  - Активная подписка Azure.
  - Центр Интернета вещей Azure в подписке;
  - клиентское приложение, которое отправляет сообщения в Центр Интернета вещей Azure.
- Git. ([Скачать Git.](https://www.git-scm.com/downloads))

## <a name="create-an-azure-web-app"></a>Создание веб-приложения Azure

1. На [портале Azure](https://ms.portal.azure.com/) щелкните **Создать** > **Интернет+мобильные устройства** > **Веб-приложение**.
1. Введите уникальное имя задания, проверьте подписку, укажите группу ресурсов и расположение, а затем выберите **Закрепить на панели мониторинга** и нажмите кнопку **Создать**.

   Мы рекомендуем выбрать то расположение, в котором находится группа ресурсов. Это позволит оптимизировать скорость обработки и снизить затраты на передачу данных.

   ![Создание веб-приложения Azure](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Настройка веб-приложения для чтения данных датчиков из Центра Интернета вещей

1. Откройте только что подготовленное веб-приложение.
1. Щелкните **Параметры приложения**, а затем в разделе **Параметры приложения** добавьте приведенные ниже пары "ключ — значение".

   | Ключ                                   | Значение                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Получено из обозревателя Центра Интернета вещей                                |
   | Azure.IoT.IoTHub.DeviceId             | Получено из обозревателя Центра Интернета вещей                                |
   | Azure.IoT.IoTHub.ConsumerGroup        | Имя группы потребителей, добавленной в Центр Интернета вещей.  |

   ![Добавление параметров веб-приложении с помощью пар "ключ — значение"](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Отправка созданного веб-приложения для размещения в Azure

Теперь веб-приложение доступно в GitHub. В нем отображаются данные, поступающие от датчиков в реальном времени, из Центра Интернета вещей. Далее нам нужно настроить работу веб-приложения с репозиторием Git, скачать веб-приложение из GitHub и отправить его для размещения в Azure.

1. В веб-приложении щелкните **Варианты развертывания** > **Выбор источника** > **Локальный репозиторий Git**.

   ![Настройка веб-приложения Azure для использования локального репозитория Git](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

1. Щелкните **Настройка подключения**, укажите имя пользователя и пароль, которые будут использоваться для подключения к репозиторию Git в Azure, а затем нажмите кнопку **ОК**.

   ![Указание имени пользователя и пароля репозитория Git в Azure для веб-приложения](media/iot-hub-live-data-visualization-in-web-apps/6_web-app-set-user-password-git-repo-azure.png)

1. Чтобы завершить настройку, нажмите кнопку **ОК**.
1. Щелкните **Обзор** и запишите значение параметра **URL-адрес клона Git**.

   ![Получение URL-адреса клона Git для веб-приложения Azure](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

1. Откройте окно терминала или командной строки на локальном компьютере.
1. Скачайте веб-приложение из GitHub и отправьте его для размещения в Azure. Для этого выполните следующие команды.

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!Note]
   > Значение параметра \<URL-адрес клона Git\> — это URL-адрес репозитория Git, полученный на странице **Обзор** веб-приложения.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Открытие веб-приложения, чтобы просмотреть данные о температуре и влажности, полученные в реальном времени, в Центре Интернета вещей

На странице **Обзор** щелкните URL-адрес, чтобы открыть веб-приложение.

![Получение URL-адреса для веб-приложения Azure](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Данные о температуре и влажности, полученные в реальном времени, должны отображаться в Центре Интернета вещей.

![Страница веб-приложения Azure с данными о температуре и влажности, полученными в реальном времени](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

## <a name="next-steps"></a>Дальнейшие действия
Вы успешно использовали веб-приложение Azure для визуализации данных датчика, полученных в реальном времени, из Центра Интернета вещей Azure.

Но это не единственный способ для визуализации данных из Центра Интернета вещей Azure. Дополнительные сведения см. в статье [Visualize real-time sensor data from Azure IoT Hub using Power BI](iot-hub-live-data-visualization-in-power-bi.md) (Использование Power BI для визуализации данных, получаемых Центром Интернета вещей от датчика в режиме реального времени).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
