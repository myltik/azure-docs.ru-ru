---
title: Пользовательский интерфейс Служб мобильного взаимодействия Azure — практическое руководство по рекламным кампаниям
description: Обзор пользовательского интерфейса для Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0c536d378023ee4a9675bb2d67262fb485c8052b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Начало работы с push-уведомлениями и управление ими для взаимодействия с конечными пользователями
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

После полной интеграции пакета SDK в приложение вы можете использовать раздел «Рекламная компания» в пользовательском интерфейсе для отправки push-уведомлений пользователям приложения.  

## <a name="do-your-first-push-notification-campaign"></a>Создание первой кампании push-уведомлений
* Убедитесь, что модуль обработки рекламных кампаний интегрирован в приложение с пакетом SDK. 
* Выберите приложение.

![First1][1]

* Перейдите в раздел "Охват" и нажмите кнопку "Создать объявление".

![First2][2]

* Создайте новую кампанию и назовите ее.
  
![First3][3]

* Укажите способ доставки уведомления "Только в приложении".

![First4][4]

* Создайте сообщение, которое нужно отправить.

![First5][5]

* Вы можете озаглавить уведомление (необязательно).
* Добавьте содержимое push-уведомления.
* Вы можете отправить изображение. Обратите внимание, что размер файла не может превышать 32 768 байт.
* Вы также можете выбрать дополнительные параметры, но это описано далее в этом учебнике.
* Выберите тип содержимого "Только уведомление".

![First6][6]

* Создайте кампанию push-уведомлений, и она отобразится в списке кампаний.

![First7][7]

## <a name="test-your-push-notification-campaign"></a>Тестирование кампании push-уведомлений
![Test1][8]

* Зарегистрируйте устройство.
* Установите флажок напротив устройства, на которое нужно отправить push-уведомление.
* Нажмите кнопку "Проверить", чтобы отправить push-уведомление на устройство.

![Test2][9]

* Активация кампании

![Test3][10]

* Теперь, когда вы создали кампанию, нужно просто активировать ее для отправки уведомления пользователям.

## <a name="send-personalized-pushes"></a>Отправка персонализированных push-уведомлений
* В этом примере создается push-уведомление, в котором вводится пользовательский код ретробонуса.

![Personalize1][11]

Персонализация производится путем замены маркера маркером из тега сведений о приложении, поэтому вам нужно будет сначала убедиться, что у пользователя определены правильные сведения о приложении. В этом примере для целевых пользователей определяется тег сведений о приложении с именем rebate_code.
Как видно выше, в содержимое push-уведомления входит маркер ${rebate_code}, который укажет, что его нужно заменить на фактическое содержимое тега сведений о приложении.

> [!WARNING]
> Если тег сведений о приложении не определен для пользователя, пользователь не получит push-уведомление.

* Результат

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Вы можете дополнительно персонализировать текст уведомления,
![Personalize3][13]

* включая заголовок уведомления
* и содержимое сообщения.
* Выберите тип объявления (текстовое представление или веб-представление)

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Текст объявления можно также персонализировать с помощью:
* URL-адреса действия, если нужно настроить целевую страницу;
* заголовка;
* текста сообщения.

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Дифференциация push-уведомления (в приложении или за его пределами)
* Укажите тип отправляемого уведомления, приложение, перейдите в раздел "Охват", выберите или создайте кампанию push-уведомлений и перейдите в раздел "Уведомления".
* Щелкните нужный "режим доставки".
* Установите флажок "Ограничить действия", если нужно, чтобы уведомление происходило при определенных действиях (на определенных экранах).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Режим доставки "Только за пределами приложения"
![Differentiate2][16]

Режим доставки "Только за пределами приложения" предоставляет push-уведомление при закрытии приложения. Это стандартное push-уведомление.
При выборе режима "Только за пределами приложения" вы должны заранее предоставить сертификаты с платформы, на основе которых создается приложение (APNS или GCM).

### <a name="see-also"></a>См. также
* [Служба push-уведомлений Apple — сертификаты](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging — сертификат](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>Режим доставки "Только в приложении"
![Differentiate3][17]

Режим доставки "Только в приложении" предоставляет push-уведомление, если приложение выполняется.
Для этого приложения не требуется проходить систему APNS и GCM.
Для охвата пользователей можно использовать систему доставки в приложении.
Вы можете полностью настроить уведомление и решить, при каком действии (на каком экране) появится уведомление.

### <a name="anytime-delivery-mode"></a>Режим доставки "В любое время"
Режим доставки "В любое время" позволяет охватить пользователей независимо от того, запущено приложение или нет.
При выборе режима "В любое время" необходимо заранее предоставить сертификаты с платформы, на которой создается приложение (APNS или GCM). 

## <a name="schedule-a-push-campaign"></a>Планирование кампании push-уведомлений
### <a name="plan-to-start-a-campaign"></a>Планирование запуска кампании
![Shedule1][18]

Сегодня 21 марта, и вы запланировали объявление на 22 марта в полночь. Вам не нужно находиться в интерфейсе для отправки push-уведомления! Вы можете заранее запланировать минуту отправки уведомлений.

* Снимите флажок "Нет" и выберите время начала. 
* Выберите дату и время запуска кампании push-уведомлений.

### <a name="plan-to-end-a-campaign"></a>Планирование окончания кампании
![Shedule2][19]

Вам нужно остановить кампанию 25 марта в 15:00, но вы знаете, что у вас не будет возможности сделать это.
Вам не нужно находиться в интерфейсе для отправки push-уведомления! Вы можете заранее запланировать минуту окончания кампании.

* Установите флажок "Нет" или выберите время окончания.
* Выберите дату и время окончания кампании push-уведомлений.

### <a name="end-a-campaign-manually"></a>Завершение кампании вручную
![Shedule3][20]

По умолчанию флажки "Нет" установлены.
Это означает, что кампания начнется сразу после ее активации и закончится после ее остановки в разделе "Охват".

> [!NOTE]
> Кампании, созданные без даты окончания, хранят push-уведомления на устройстве локально и отображают их при следующем входе в приложение, даже если кампания завершена вручную.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Усовершенствование push-уведомления с помощью текстового представления
### <a name="what-is-a-text-view"></a>Что такое текстовое представление?
![TextView1][21]

Текстовое представление — это всплывающее окно с текстовым содержимым. Это всплывающее окно отображается после того, как пользователь щелкнет push-уведомление.
Текстовое представление позволяет предоставить дополнительное содержимое пользователю. Оно также позволяет предоставить призыв к действию, например, переход на страницу приложения, перенаправление в Магазин, открытие веб-страницы, отправка электронного письма, запуск геолокального поиска и т. д...

### <a name="example-text-view"></a>Пример: текстовое представление
* Создайте кампанию push-уведомлений в разделе "Охват" и назовите ее.

![TextView2][22]

* Напишите сообщение, которое будет отображаться в уведомлении.
* Выберите тип содержимого объявления "текст"

![TextView3][23]

> [!NOTE]
> При отправке текстового представления сначала всегда отправляется уведомление. 

* Введите текст (после выбора содержимого текстового объявления появится подраздел, в котором можно ввести отображаемый текст).

![TextView4][24]

* Напишите заголовок, который появится вверху сообщения.
* Введите основное содержимое текстового представления.
* Напишите содержимое, которое будет отображаться на кнопке действия (кнопка действия позволяет приложению создать специальное действие, например, открытие страницы приложения, перенаправление в Магазин приложений или в другие источники, которые вы можете предоставить).
* Напишите содержимое, которое будет отображаться на кнопке выхода (щелкните кнопку выхода, чтобы закрыть текстовое представление).
* Создайте кампанию push-уведомлений, чтобы она появилась в списке кампаний.

![TextView5][25]

* Активируйте кампанию push-уведомлений, чтобы отправить текстовое представление пользователям.

![TextView6][26]

* Результат

![TextView7][27]

* Пользователь получает уведомление и щелкает его.
* Текстовое представление отображается в виде всплывающего уведомления, позволяя пользователю взаимодействовать с ним.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Улучшение push-уведомления с помощью веб-представления
### <a name="what-is-a-web-view"></a>Что такое веб-представление?
![WebView1][28]

Веб-представление — это всплывающее окно с веб-содержимым. Это всплывающее окно отображается при щелчке пользователя на push-уведомление.
Веб-представление позволяет больше взаимодействовать с пользователем.
Оно также позволяет предоставить призыв к действию, например, переход на страницу приложения, перенаправление в Магазин, открытие веб-страницы, отправка электронного письма, запуск геолокального поиска и т. д...

### <a name="example-web-view"></a>Пример: веб-представление
* Создайте кампанию push-уведомления в разделе "Охват" и назовите ее.

![WebView2][29]

* Напишите сообщение, которое будет отображаться в уведомлении.
* Выберите тип содержимого "веб-содержимое"

![WebView3][30]

### <a name="about-announcement-types"></a>Сведения о типах объявлений
* Только уведомление. Это простое стандартное уведомление. Это значит, что если пользователь щелкнет его, не отобразится никакое дополнительное представление, а будет только выполнено действие, связанное с ним.
* Текстовое объявление. Это уведомление, при появлении которого пользователь может посмотреть текстовое представление.
* Веб-объявление. Это уведомление, при появлении которого пользователь может посмотреть веб-представление.
  Выберите содержимое "Веб-объявление".

> [!NOTE]
> При отправке веб-представления сначала всегда отправляется уведомление.

* Введите веб-содержимое (после выбора содержимого веб-представления появится подраздел, в котором можно ввести отображаемое содержимое веб-представления).

![WebView4][31]

* Напишите заголовок, который будет отображаться вверху сообщения (необязательно).
* Введите здесь HTML-код.
* Нажмите кнопку режима изменения источника, чтобы переключить выпуск и увидеть, как он выглядит.
* Напишите содержимое, которое отобразится на кнопке действия (кнопка действия позволяет приложению выполнить определенное действие, например, открытие страницы приложения, перенаправление в Магазин или в другой источник, который вы можете предоставить).
* Напишите содержимое, которое отобразится на кнопке выхода (нажмите кнопку выхода, чтобы закрыть веб-представление).
* Результат

![WebView5][32]

* Пользователь получает сообщение и щелкает его.
* Текстовое представление отображается в виде всплывающего уведомления, позволяя пользователю взаимодействовать с ним.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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

