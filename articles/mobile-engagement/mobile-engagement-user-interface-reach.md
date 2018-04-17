---
title: Пользовательский интерфейс Служб мобильного взаимодействия Azure - Рекламные кампании
description: Узнайте, как взаимодействовать с пользователями приложения с помощью push-уведомлений на базе Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d999b83df7d9d467f08ce8ec72468c738e8acfa5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Взаимодействие с пользователями приложения с помощью push-уведомлений
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

В этой статье описывается вкладка **Reach** (Рекламная кампания) портала **Служб мобильного взаимодействия**. Портал **Служб мобильного взаимодействия** используется для отслеживания мобильных приложений и управления ими. Обратите внимание, что для того, чтобы начать пользоваться порталом, сначала необходимо создать учетную запись **Служб мобильного взаимодействия Azure**. Дополнительные сведения см. в статье [Создание приложения Служб мобильного взаимодействия Azure](mobile-engagement-create.md).

Раздел рекламных кампаний пользовательского интерфейса — это инструмент управления кампанией push-уведомлений, где можно создавать, менять, активировать, завершать, выполнять мониторинг и статистический анализ кампаний push-уведомлений и функций, которые также доступны через Reach API (и некоторые элементы API push-уведомлений низкого уровня). Учтите, что независимо от того используете ли вы API или пользовательский интерфейс, чтобы использовать рекламные кампании, вам будет необходимо интегрировать Службы мобильного взаимодействия Azure и функцию рекламных кампаний с помощью пакета SDK в приложение для каждой платформы.

> [!NOTE]
> Многие части интерфейса портала **Служб мобильного взаимодействия** содержат кнопку **Показать справку**. Нажмите эту кнопку, чтобы получить больше контекстной информации по соответствующему разделу.
> 
> 

## <a name="four-types-of-push-notifications"></a>Четыре типа push-уведомлений
1. Объявления — позволяют отправлять пользователям рекламные сообщения, которые перенаправляют их в другое расположение в приложении, или отправлять рекламные сообщения на веб-страницы или в магазины вне приложения. 
2. Опросы — позволяют собирать информацию от конечных пользователей, задавая им вопросы.
3. Отправка данных — позволяет вам отправлять файлы двоичных данных и файлы данных Base64. Информация, содержащаяся в отправленных данных отправляется в ваше приложение для изменения текущей работы пользователей в приложении. В вашем приложении должна быть возможность обработки данных в отправленных данных.

## <a name="campaign-details"></a>Информация о кампании
Можно изменять, клонировать, удалять или активировать неактивированные кампании, наведя указатель мыши на их имена, или щелкнуть их, чтобы открыть. Можно клонировать уже активированные кампании, наведя указатель мыши на их имена, или щелкнуть их, чтобы открыть. Однако после активирования кампанию невозможно изменить.

![Reach1][18]

## <a name="reach-feedback"></a>Отклик на рекламную кампанию
Щелкните пункт **Статистика**, чтобы просмотреть сведения о рекламной кампании. **Простое** представление — это столбчатая диаграмма, показывающая, что произошло после активации кампании. **Расширенное** представление содержит более подробные сведения о кампании push-уведомлений. При отправке тестовой кампании (т. е. отправке push-уведомления на тестируемое устройство) эти сведения не отображаются. Эти сведения надлежит интерпретировать следующим образом:

1. **Переданные** — это число сообщений, отправленных на устройства. Это число зависит от целевой аудитории, указанной при создании кампании push-уведомлений. Если целевая аудитория не указана, push-уведомление будет отправлено на все зарегистрированные устройства. Как и другие службы push-уведомлений, мы не отправляем уведомления непосредственно на устройства, а передаем их в соответствующие платформе службы push-уведомлений (PNS - APNS/GCM/WNS), чтобы те могли доставить уведомления на устройства. 
2. **Доставленные** — это число сообщений, успешно доставленных службой push-уведомлений на устройство и признанных полученными пакетом SDK для Служб мобильного взаимодействия. 
   
   *Почему число доставленных сообщений может быть меньше числа отправленных:*
   
   1. Если пользователь отменил установку приложения на устройство, но на момент отправки push-уведомления служба push-уведомлений об этом не знает, сообщение удаляется.
   2. Если на устройстве есть приложение, но в течение долгого времени устройство работает автономно, служба push-уведомлений не сможет доставить сообщение на устройство. 
   3. Если сообщение доставлено на устройство, но пакет SDK для Служб мобильного взаимодействия в приложении не распознал его содержание, сообщение удаляется. Это может произойти, если настройка уведомления в приложении создает исключение, которое мы перехватили в SDK, и привела к удалению сообщения. Подобная проблема также возникает, если приложение на устройстве использует версию SDK для Служб мобильного взаимодействия, которая не может распознать более новую версию push-уведомления, отправленного с платформы, но это возможно только в том случае, если приложение было обновлено после отправки уведомления с платформы службы. На вкладке **Дополнительно** будет указано количество удаленных сообщений. 
   4. На устройства iOS сообщения могут не доставляться, если уровень заряда аккумулятора слишком низкий или при обработке удаленных уведомлений приложение потребляет слишком много ресурсов. Это — ограничение устройств iOS.   
3. **Отображенные** — число сообщений, показанных пользователю приложения на устройстве в виде всплывающих уведомлений в центре уведомлений или в самом мобильном приложении.  На вкладке **Дополнительно** будет указано количество системных уведомлений и уведомлений в приложении. 
   
   *Причины, по которым отображаемое количество меньше доставленного (которое еще будет отображено)*
   
   1. Если у кампании уведомлений есть дата окончания, то возможно, что уведомление было доставлено, но когда пришло время открыть и отобразить его пользователю приложения, срок действия кампании уже закончился, поэтому оно так и не было показано.   
   2. Если уведомление представляет собой уведомление в приложении, то оно отображается только в том случае, если пользователь открыл приложение. В случаях, когда пользователь не открыл приложение, пакет SDK сообщит, что уведомление было доставлено, но не будет открыто, пока пользователь не откроет приложение. 
   3. Если уведомление представляет собой уведомление в приложении и оно привязано к конкретному действию или окну, то оно также будет считаться доставленным, но не показанным, пока пользователь не откроет необходимое окно в приложении. 
4. **Количество взаимодействий с пользователем** — указывает количество сообщений, с которыми взаимодействовал пользователь приложения. Это количество будет включать сообщения, которые были закрыты и для которых были выполнены действия. 
   
   * *Пользователь приложения может взаимодействовать с уведомлением одним из следующих способов:*
     
     1. Если уведомление является системным или уведомление в приложении отправляется как простое уведомление, пользователь приложения щелкает это уведомление.
     2. Если это уведомление в приложении с текстом, веб-представлением или опросом, пользователь приложения нажимает кнопку действия в области уведомлений.
     3. Если это уведомление в приложении с веб-представлением, пользователь приложения щелкает URL-адрес в веб представлении (только на устройствах Android).
   * *Пользователь приложения может выйти из уведомления одним из следующих способов:*
     
     1. Нажав кнопку "Закрыть" в самом уведомлении. 
     2. Смахнув или удалив уведомление. 
     3. Уведомления в приложении с текстом или веб-содержимым и опросами обычно отображаются пользователям приложения в два этапа. Сначала пользователь видит уведомление, а после щелчка по уведомлению — последующий текст, веб-содержимое или опрос. Пользователь приложения может выйти из уведомления любым из указанных выше способов. Соответствующие данные появятся на вкладке "Дополнительно". 
5. **Выполненные** — указывает количество сообщений, для которых пользователь явно выполнил действия. Это — наиболее интересный показатель, указывающий, сколько пользователей приложения заинтересовались переданным вами push-уведомлением. 

> [!NOTE]
> Если пользователь платформы iOS или Windows открывает приложение и кампания не включает временных ограничений, системное уведомление и уведомление в приложении могут появиться одновременно. В результате число отображенных сообщений может оказаться выше числа доставленных. Если пользователь взаимодействует с уведомлением или предпринимает какие-то действия, то даже число взаимодействий может быть выше числа доставленных уведомлений. 
> 
> 

![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

