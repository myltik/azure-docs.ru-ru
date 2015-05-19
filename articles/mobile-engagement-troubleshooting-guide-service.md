<properties 
   pageTitle="Руководство по устранению неполадок Azure Mobile Engagement — Служба" 
   description="Руководства по устранению неполадок для Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>


# Поиск и устранение неполадок в службе

Ниже представлены возможные проблемы в работе Azure Mobile Engagement.

## Простои службы.

### Проблема
- Проблемы, которые предположительно связаны с простоями службы Azure Mobile Engagement.

### Причины
- Проблемы, которые предположительно связаны с простоями службы Azure Mobile Engagement, могут возникать по таким причинам:
    - Изолированные проблемы, которые изначально носят системный характер для всех компонентов Azure Mobile Engagement.
    - Известные проблемы, вызванные сбоем сервера \(это не всегда отображается в состоянии сервера\):
	- Задержки планирования, ошибки определения цели, проблемы обновления индикаторов событий, прекращение сбора статистических данных, прекращение отправки push-уведомлений, прекращение работы API, невозможность создать новые приложения или новых пользователей, ошибки DNS и ошибки времени ожидания в пользовательском интерфейсе, API или приложениях на устройстве.
    - Простои, связанные с облаком: [Состояние службы Azure](http://status.azure.com/), [Состояние веб-служб Amazon Web Services](http://status.aws.amazon.com/) 
    - Простои, связанные со службами push-уведомлений: [Google — служба](http://www.google.com/appsstatus#hl=en&v=status), [Apple — служба](http://www.apple.com/support/systemstatus/), [Android — Google GCM](http://developer.android.com/google/gcm/index.html), [Android — Amazon ADM](https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple — APNS](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone — WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone — MPNS][LinkMPNS], [Windows — WNS](https://developer.windows.com/)
    - Перерывы в работе магазина приложений: [GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone Store](http://www.windowsphone.com/), [Windows Store](http://windows.microsoft.com/)

*Чтобы проверить и узнать, является ли проблема системной, вы можете протестировать одну и ту же функцию с помощью разных объектов:*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*Чтобы проверить, влияет ли проблема только на пользовательский интерфейс или API:*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[Документация по API][Link 4], [Документация по пользовательскому интерфейсу][Link 1]

*Чтобы проверить, заключается ли проблема в сети мобильного телефона:*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*Чтобы проверить, заключается ли проблема в устройстве:*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Документация по пользовательскому интерфейсу — Настройки][Link 1]

*Чтобы проверить, заключается ли проблема в приложении:*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

Чтобы проверить, касается ли проблема обновления операционных систем на устройствах конечных пользователей, в случае чего требуется обновить пакет SDK для устранения проблемы:

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[Руководство по устранению неполадок — пакет SDK][Link 2]
 
## Проблемы, связанные с подключением и отображением неправильной информации.

### Проблема
- Проблемы со входом в пользовательский интерфейс Azure Mobile Engagement.
- Ошибки подключения к API Azure Mobile Engagement.
- Проблемы загрузки тегов информации о приложении через API устройства.
- Проблемы со скачиванием журналов или экспортированных данных из Azure Mobile Engagement.
- В пользовательском интерфейсе Azure Mobile Engagement показана неправильная информация.
- В журналах Azure Mobile Engagement показана неправильная информация.

### Причины
- При наличии проблем с подключением в Azure Mobile Engagement:
    - Убедитесь, что у учетной записи достаточно разрешений для выполнения задачи.
    - Убедитесь, что проблема не изолирована на одном компьютере или в вашей локальной сети.
    - Убедитесь, что служба Azure Mobile Engagement не сообщала о простоях.
    - Убедитесь, что при создании файлов тегов информации о приложении соблюдены все следующие правила.
        - Используйте только кодировку UTF8 \(кодировка ANSI не поддерживается\).
        - Используйте запятую «,» как знак разделения \(можно отправить запрос на обслуживание, чтобы запросить изменение знака разделения запятой «,» в CSV-файле на другой символ, например на точку с запятой «;»\).
        - Используйте только строчные буквы для логических значений true и false.
        - Используйте файл, размер которого меньше, чем максимальный размер файла — 35 МБ.

### Дополнительные материалы

[Документация по API][Link 4], [Документация по пользовательскому интерфейсу — Домашняя страница][Link 1]
 
## Запросы функций.

### Проблема
- Функция, которую вы хотите использовать, еще не существует в Azure Mobile Engagement.

### Причины

Чтобы предложить новую функцию для Azure Mobile Engagement, отправьте запрос на обслуживание Azure Mobile Engagement с максимально детальной информацией о том, какую новую функцию вы хотели бы видеть в Azure Mobile Engagement.

### Дополнительные материалы

[Отзывы Mobile Engagement/Запросы функций](http://feedback.azure.com/forums/285737-mobile-engagement)

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
[LinkMPNS]: http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx

<!--HONumber=54-->