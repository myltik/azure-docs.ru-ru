---
title: Проверка подлинности на основе токенов (HTTP/2) для APNS в Центрах уведомлений Azure | Документация Майкрософт
description: В этом разделе объясняется, как использовать новую проверку подлинности по токенам для APNS
services: notification-hubs
documentationcenter: .net
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: ca86130e9c184576fc44119190d6224a363c6561
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777898"
---
# <a name="token-based-http2-authentication-for-apns"></a>Проверка подлинности на основе токенов (HTTP/2) для APNS
## <a name="overview"></a>Обзор
В этой статье подробно описывается использование нового протокола APNS, HTTP/2, при проверке подлинности на основе токенов.

Основные преимущества использования нового протокола:
-   Проще создать токен, чем сертификат.
-   У токенов отсутствует срок окончания действия. Вы управляете их созданием и отзывом.
-   Полезные данные могут иметь размер до 4 КБ.
- Синхронная обратная связь.
-   Используется последняя версия протокола Apple, в то время как сертификаты по-прежнему используют устаревший двоичный протокол.

Этот новый механизм можно реализовать в два этапа за несколько минут:
1.  Получите необходимые сведения на портале учетной записи разработчика Apple.
2.  Добавьте новые данные в центр уведомлений.

Теперь для APNS центры уведомлений будут использовать новую систему проверки подлинности. 

Если для APNS вы использовали учетные данные сертификата, обратите внимание на следующее:
- свойства токена перезапишут сертификат в нашей системе;
- приложение по-прежнему продолжит получать уведомления.

## <a name="obtaining-authentication-information-from-apple"></a>Получение сведений о проверке подлинности от Apple
Чтобы включить проверку подлинности на основе токенов, необходимы следующие свойства вашей учетной записи разработчика Apple:
### <a name="key-identifier"></a>Идентификатор ключа
Идентификатор ключа можно получить на странице "Ключи" в учетной записи разработчика Apple.

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Идентификатор и имя приложения
Имя приложения доступно на странице идентификаторов приложений в учетной записи разработчика. 
![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Идентификатор приложения доступен на странице сведений о членстве в учетной записи разработчика.
![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)


### <a name="authentication-token"></a>Токен проверки подлинности
После создания токена приложения можно скачать токен проверки подлинности. Дополнительные сведения о создании этого токена см. в [документации для разработчиков Apple](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Настройка центра уведомлений для использования проверки подлинности на основе токенов
### <a name="configure-via-the-azure-portal"></a>Настройка на портале Azure
Чтобы включить проверку подлинности на основе токенов, войдите на портал Azure и перейдите на панель "Концентратор уведомлений > Службы уведомлений > APNS". 

Там есть новое свойство *Режим проверки подлинности*. Выберите "Токен", чтобы обновить все соответствующие свойства токенов в концентраторе.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

- Введите свойства, полученные из учетной записи разработчика Apple. 
- Выберите режим приложения (Production (Рабочий) или Sandbox (Песочница)). 
- Щелкните Save (Сохранить), чтобы обновить учетные данные APNS. 

### <a name="configure-via-management-api-rest"></a>Настройка с помощью API управления (REST)

Чтобы настроить центр уведомлений для использования проверки подлинности на основе токенов, вы можете воспользоваться нашими [API-интерфейсами управления](https://msdn.microsoft.com/library/azure/dn495827.aspx).
В зависимости от выбранного режима для настраиваемого приложения (Sandbox (Песочница) или Production (Рабочее)) (указано в учетной записи разработчика Apple), используйте одну из соответствующих конечных точек.

- Конечная точка песочницы: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
- Рабочая конечная точка: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Для проверки подлинности на основе токенов требуется версия API **2017-04 или более поздняя**.
> 
> 

Ниже приведен пример запроса PUT, с помощью которого можно настроить центр для использования проверки подлинности на основе токенов:


        PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
          "Properties": {
            "ApnsCredential": {
              "Properties": {
                "KeyId": "<Your Key Id>",
                "Token": "<Your Authentication Token>",
                "AppName": "<Your Application Name>",
                "AppId": "<Your Application Id>",
                "Endpoint":"<Sandbox/Production Endpoint>"
              }
            }
          }
        

### <a name="configure-via-the-net-sdk"></a>Настройка с использованием пакета SDK для .NET
Центр можно настроить для использования проверки подлинности на основе токенов с помощью нашего [клиентского пакета SDK последней версии](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8). 

Ниже приведен пример кода, который иллюстрирует правильное использование.


        NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
        string token = "YOUR TOKEN HERE";
        string keyId = "YOUR KEY ID HERE";
        string appName = "YOUR APP NAME HERE";
        string appId = "YOUR APP ID HERE";
        NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
        desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
        desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
        nm.UpdateNotificationHubAsync(desc);

## <a name="reverting-to-using-certificate-based-authentication"></a>Восстановление проверки подлинности на основе сертификатов
С помощью любого предыдущего метода можно в любое время вернуться к использованию проверки подлинности на основе сертификатов и передавать сертификат вместо свойств токена. Это действие перезаписывает ранее сохраненные учетные данные.
