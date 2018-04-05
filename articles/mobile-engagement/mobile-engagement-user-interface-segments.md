---
title: Пользовательский интерфейс Служб мобильного взаимодействия Azure — сегменты
description: Научитесь создавать сегменты и управлять ими с помощью Служб мобильного взаимодействия Azure для выявления закономерностей использования
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6a4f2205-4a3c-406e-a04f-5e6f2a36653f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 53c5b1b672a0d7212a3d298c3e411a9cc6ac0807
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Создание сегментов пользователей и управление ими для выявления закономерностей
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

В этой статье описывается вкладка **Сегменты** портала **Служб мобильного взаимодействия**. Портал **Служб мобильного взаимодействия** используется для отслеживания мобильных приложений и управления ими.

С помощью раздела "Сегменты" пользовательского интерфейса можно сегментировать пользователей на основе разного поведения и аналитических данных, которые можно получить из приложения, а также через Segments API. Сегменты впервые вычисляются через 24 часа после создания и затем через каждые 24 часа на основе последней аналитической информации. После вычисления сегмента по нему ежедневно отображается диаграмма «Ежедневный журнал».

> [!NOTE]
> Многие части интерфейса портала **Служб мобильного взаимодействия** содержат кнопку **Показать справку**. Нажмите эту кнопку, чтобы получить больше контекстной информации по соответствующему разделу.
> 
> 

## <a name="create-segments"></a>Создание сегментов
В разделе «Аналитика» можно создать сегмент на основе не более 10 критериев на определенный период до 60 прошедших дней. Например, можно создать сегмент на основе количества людей, которые просматривали конкретные страницы или искали определенное содержимое в приложении за последние 10 дней. Эта информация доступна в разделе «Аналитика». Таким образом, с ее помощью можно создать сегмент, а затем настроить push-уведомление для отслеживания этого подмножества пользователей, чтобы они вернулись в приложение. 

> [!NOTE]
> После вычисления сегмент нельзя изменять. Его можно только клонировать (копировать) или уничтожить (удалить). Сегмент можно клонировать в пределах одного приложения (с одним и тем же идентификатором) и в другие приложения (с другим идентификатором). 

 ![segments1][35] 

## <a name="examples-segments"></a>Примеры сегментов
 ![segments2][36]

Сегменты позволяют сегментировать конечных пользователей приложения.
Сегментирование пользователей — важная маркетинговая стратегия. Службы мобильного взаимодействия Azure позволяют получать данные журнала и создавать настраиваемые сегменты. С помощью этого полнофункционального инструмента можно узнать о работе клиентов в вашем приложении. Вы можете без труда анализировать сегменты и отправлять по ним push-уведомления.
Сегментирование зачастую используют, если необходимо отправить push-уведомления, побуждающие пользователей оценить ваше приложение в магазине. Вместо того, чтобы отправлять уведомления всем пользователям, вы можете создать сегмент, в котором будут указаны только те пользователи, которые ежедневно использовали ваше приложение за последний месяц и получили очень хорошие результаты при работе с ним. При отправке меньшего количества push-уведомлений, цели которых легче достичь, повышается рентабельность инвестиций.

 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>На базе основных элементов Служб мобильного взаимодействия Azure можно создавать следующие сегменты:
* Событие. Создайте сегмент, взяв за цель одно конкретное событие приложения, происходящее более чем дважды в неделю. 
* Сеанс. Создайте сегмент пользователей, которые использовали приложение более 5 раз за прошлую неделю.
* Действие. Создайте сегмент пользователей, которые использовали одну страницу или одно и то же содержимое более 10 раз за последний месяц.
* Задание. Создайте сегмент пользователей, которые выполнили одно задание более чем дважды за день.
* Сбой. Создайте сегмент пользователей, у которых сбой произошел более 10 раз за прошлую неделю. (Вы можете отправить сегмент с извинениями или даже купоном!)
* Ошибка. Создайте сегмент пользователей, у которых ошибка произошла более 100 раз за последние 3 дня.
* Информация о приложении. Создайте сегмент, целью которого будет информация о приложении с данными о том, что происходило в приложениях пользователей в течение последних 25 дней.
  
  ![segments4][38]

Чтобы оптимально использовать сегмент, необходимо заранее выполнить настраиваемую интеграцию пакета SDK в приложении, используя план добавления тега «Информация о приложении».
Перейдите на домашнюю страницу интерфейса, выберите необходимое приложение и щелкните раздел «Сегменты».

1. Выберите раздел «Сегменты».
2. Нажмите кнопку «Создать сегмент», чтобы создать сегмент.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Пример из практики: создание простого сегмента на основе информации в разделе «Сеанс»
Создайте сегмент всех пользователей, которые использовали ваше приложение по крайней мере 50 раз за прошлую неделю. В нем найдите только тех пользователей, которые работали в приложении по крайней мере 30 секунд за сеанс. При этом будут отображены все пользователи с положительным результатом работы в приложении. Затем созданный сегмент можно использовать для отправки этим пользователям push-уведомлений с просьбой оценить ваше приложение в магазине.

 ![segments5][39]

1. Присвойте сегменту имя для быстрого поиска в списке «Сегмент».
2. Нажмите кнопку «Создать».
   
   ![segments6][40]

Выберите «Сеанс».

 ![segments7][41]

1. Выберите период «На прошлой неделе».
2. Нажмите кнопку «Далее».
   
   ![segments8][42]
3. Выберите в списке нужный оператор: =; ≥, ≤.
4. Введите необходимое количество в поле «Количество».
5. Выберите необходимое значение в поле «Периодичность». 
6. Нажмите кнопку «Далее».
   В этом примере заданы пользователи, которые провели по крайней мере 50 сеансов на прошлой неделе.
   
   ![segments9][43]

Для сегментации сеанса можно выбрать время для каждого сеанса в качестве критерия.

1. Выберите оператора из списка.
2. Укажите продолжительность каждого сеанса в поле «Время».
3. Нажмите кнопку Далее.
   В этом примере указано, что исходя из всех сеансов, сегментированных по разделу периодичности, должны быть выбраны только те пользователи, которые провели сеансы продолжительностью более 30 секунд каждый.
   
   ![segments10][44]

Укажите имя своего критерия, чтобы получить его в полной воронке, и щелкните «Готово».

 ![segments11][45]

После завершения настройки критерий будет отображаться в воронке сегмента.
Так как сегмент основывается на аналитических данных, вычисление сегментов происходит один раз в день.
В этом примере 47,7 % общего количества пользователей соответствует критерию. Это должны быть пользователи, которые получили очень хорошие результаты работы и с большей вероятностью дадут высокую оценку, если им отправить push-уведомление с просьбой оценить приложение в магазине.

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

