---
title: Пользовательский интерфейс Служб мобильного взаимодействия Azure - Настройки
description: Узнайте, как управлять глобальными параметрами приложения с помощью Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 858f4cb4-14de-4bb5-826f-28cadbfc928b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 31df9f151febcb7e5f0f2cd4a83053b3aac416a3
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-the-global-settings-of-your-application"></a>Управление глобальными параметрами приложения
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Параметры в меню **Параметры** , доступные для приложения, различаются в зависимости от платформы приложения и разрешений, которые вы ему предоставили. К ним относятся «Подробности», «Проекты», «Системные push-уведомления», «Скорость отправки push-уведомлений», «Пакет SDK», «Отслеживание», «Тег (информация о приложении)» и «Коммерческое давление». Параметром «Тег (информация о приложении)» можно управлять с помощью приложения (используя пакет SDK) или сервера (используя API устройства). 

> [!NOTE]
> Многие части интерфейса портала **Служб мобильного взаимодействия** содержат кнопку **Показать справку**. Нажмите эту кнопку, чтобы получить больше контекстной информации по соответствующему разделу.
> 
> 

## <a name="details"></a>Сведения
Позволяет менять имя и описание приложения, а также просматривать его владельца и разрешения, назначенные вашей роли. 

Область "Конфигурация аналитики (Analytics)" позволяет просматривать и менять день, с которого начинается неделя, и период удержания в днях.

  ![settings1][46]

## <a name="projects"></a>Проекты
Позволяет выбрать все проекты, которые необходимо отображать в приложении. 

Вы также можете найти проект и просмотреть имя, описание, владельца и разрешения своей роли для любого проекта, в который входит ваше приложение.

Дополнительные сведения см. в статье [Управление существующими приложениями и проектами][Link 13]

  ![settings3][48]

## <a name="native-push"></a>Системные push-уведомления
Позволяет удалить существующий сертификат или зарегистрировать новый сертификат, который необходимо использовать при передаче системных push-уведомлений. Системные push-уведомления позволяют Службам мобильного взаимодействия Azure отправлять push-уведомления в ваше приложение в любое время, даже если оно не работает. 

Указав учетные данные или сертификаты по крайней мере в одной службе системных push-уведомлений, вы можете выбрать «Всегда» при создании рекламных кампаний, а также использовать параметр «Уведомитель» в API push-уведомлений.

### <a name="apple-push-notification-service-apns"></a>Служба push-уведомлений Apple (APNS)
Чтобы включить системные push-уведомления, используя службу push-уведомлений Apple, вам необходимо зарегистрировать свой сертификат. Необходимо указать тип сертификата — сертификат разработки или производственный сертификат. Затем будет необходимо передать сертификат и ввести пароль.

Дополнительные сведения см. в статье [Документация по службам мобильного взаимодействия][Link 5]

![settings4][49]

### <a name="windows-push-notification-service-wpns"></a>Служба push-уведомлений Windows (WPNS)
Чтобы включить системные push-уведомления, используя службу уведомлений Windows, необходимо указать учетные данные приложения. Вам понадобятся ваши идентификатор безопасности пакета (SID) и секретный ключ.

![settings5][50]

### <a name="google-cloud-messaging-for-android-gcm"></a>Google Cloud Messaging (GCM) для Android
Чтобы включить системные push-уведомления с помощью GCM, необходимо следовать указаниям, предоставленным Google. Затем необходимо вставить простой ключ API сервера, настроенный без ограничений IP-адресов. Требуется интеграция пакета SDK для Android версии v1.12.0 или более поздней.

Дополнительные сведения можно найти в разделе  

* [Документация по службам мобильного взаимодействия][Link 5]
* [Руководство по GCM для разработчиков Google](http://developer.android.com/guide/google/gcm/gs.html)

### <a name="amazon-device-messaging-for-android-adm"></a>Amazon Device Messaging (ADM) для Android
Чтобы включить системные push-уведомления, используя ADM, необходимо указать Amazon <OAuth credentials>, которые состоят из идентификатора клиента и секрета клиента (требуется интеграция с пакетом SDK для Android версии 2.1.0 или более поздней).

Дополнительные сведения можно найти в разделе  

* [Документация по службам мобильного взаимодействия][Link 5]
* [Документация по ADM для разработчиков Amazon](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## <a name="push-speed"></a>Скорость отправки push-уведомлений
Показывает текущую скорость отправки push-уведомлений и позволяет определить скорость их передачи для приложения.

  ![settings7][52]

## <a name="tag-app-info"></a>Тег (информация о приложении)
![settings11][56]

## <a name="commercial-pressure"></a>Коммерческое давление
![settings12][57]

## <a name="see-also"></a>См. также
* [Основные понятия Azure Mobile Engagement][Link 6]
* [Поиск и устранение неполадок в службе][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md

