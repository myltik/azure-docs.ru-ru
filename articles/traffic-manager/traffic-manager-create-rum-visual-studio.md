---
title: Измерения на стороне пользователей в диспетчере трафика Azure с помощью Visual Studio Mobile Center | Документация Майкрософт
description: Настройка мобильного приложения, разработанного с использованием Visual Studio Mobile Center, для отправки реальных измерений пользователя в диспетчер трафика
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 893e84b07b365fb0b534e0ddc021b2249c4174cf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30181016"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Отправка измерений на стороне пользователей в диспетчер трафика с помощью Visual Studio Mobile Center

Чтобы настроить мобильное приложение, разработанное с помощью Visual Studio Mobile Center, для отправки измерений на стороне пользователей в диспетчер трафика, выполните такие действия:

>[!NOTE]
> В настоящее время отправка измерений на стороне пользователей в диспетчер трафика поддерживается только для Android.

Чтобы настроить измерения на стороне пользователей, необходимо получить ключ и инструментировать приложение с помощью RUM-пакета.

## <a name="step-1-obtain-a-key"></a>Шаг 1. Получение ключа
    
Измерения, которые вы принимаете и отправляете в диспетчер трафика из клиентского приложения, идентифицируются службой с помощью уникальной строки, называемой ключом измерений на стороне пользователей (RUM). Ключ RUM можно получить с помощью портала Azure, REST API или интерфейсов PowerShell и/или CLI.

Для получения ключа RUM с помощью портала Azure используйте следующую процедуру:
   1. В браузере войдите на портал Azure. Если у вас еще нет учетной записи, вы можете зарегистрироваться для получения бесплатной пробной версии на один месяц.
   2. На панели поиска портала выполните поиск по имени профиля диспетчера трафика, которое необходимо изменить, а затем щелкните профиль диспетчера трафика в отображаемых результатах.
   3. На странице профиля диспетчера трафика в разделе **Параметры** щелкните **Real User Measurements** (Измерения на стороне пользователей).
   4. Щелкните **Создать ключ**, чтобы создать ключ RUM.
        
   ![Создание ключа измерений на стороне пользователей](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Рис. 1. Создание ключа измерений на стороне пользователей**

   5.   На этой странице отображается созданный ключ RUM и фрагмент кода JavaScript, который необходимо внедрить на страницу HTML.
 
   ![Код JavaScript ключа измерений на стороне пользователей](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Рис. 2. Ключ измерений на стороне пользователей и скрипт JavaScript измерений**
 
   6. Нажмите кнопку **Копировать**, чтобы скопировать ключ RUM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Шаг 2. Инструментирование приложения с помощью пакета RUM пакета SDK Mobile Center

Если вы не знакомы с Visual Studio Mobile Center, посетите его [веб-сайт](https://mobile.azure.com). Дополнительные сведения об интеграции пакета средств разработки см. в статье [Get Started with Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android) (Начало работы с Android).

Чтобы использовать измерения на стороне пользователей, выполните следующую процедуру:

1.  Добавление пакета SDK в проект

    На этапе предварительной версии пакета SDK ATM RUM необходимо явно указать репозиторий пакета.

    В файле **app/build.gradle** добавьте следующие строки:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    В файле **app/build.gradle** добавьте следующие строки:

    ```groovy
    dependencies {   
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Запуск пакета SDK

    Откройте основной класс действия приложения и добавьте следующие инструкции импорта:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Найдите обратный вызов `onCreate` в том же файле и добавьте такой код:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Дополнительная информация
- Узнайте больше об [измерениях на стороне пользователей](traffic-manager-rum-overview.md).
- Узнайте о том, [как работает диспетчер трафика](traffic-manager-overview.md)
- Дополнительные сведения о Mobile Center см. [здесь](https://docs.microsoft.com/mobile-center/).
- [Регистрация в Mobile Center](https://mobile.azure.com)
- Узнайте больше о [методах маршрутизации трафика](traffic-manager-routing-methods.md) , поддерживаемых в диспетчере трафика.
- Узнайте, как [создать профиль диспетчера трафика](traffic-manager-create-profile.md)

