<properties 
   pageTitle="Пользовательский интерфейс Azure Mobile Engagement - Рекламные кампании" 
   description="Узнайте, как взаимодействовать с пользователями приложения с помощью push-уведомлений на базе Azure Mobile Engagement" 
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


# Взаимодействие с пользователями приложения с помощью push-уведомлений
Раздел рекламных кампаний пользовательского интерфейса — это инструмент управления кампанией push-уведомлений, где можно создавать, менять, активировать, завершать, выполнять мониторинг и статистический анализ кампаний push-уведомлений и функций, которые также доступны через Reach API (и некоторые элементы API push-уведомлений низкого уровня). Учтите, что независимо от того используете ли вы API или пользовательский интерфейс, чтобы использовать рекламные кампании, вам будет необходимо интегрировать Azure Mobile Engagement и функцию рекламных кампаний с помощью пакета SDK в приложение для каждой платформы.

### Дополнительные материалы
-  [Документация по API — API Reach][Link 4], [Документация по API — API Push][Link 4], [Руководство по устранению неполадок — push-уведомления и рекламные кампании][Link 23]
-  [Рекламные кампании: кампания][Link 27], [Рекламные кампании: критерий][Link 28], [Рекламные кампании: содержимое][Link 29], [Рекламные кампании: инструкции][Link 3]
 
## Четыре типа push-уведомлений
1.    Объявления — позволяют отправлять пользователям рекламные сообщения, которые перенаправляют их в другое расположение в приложении, или отправлять рекламные сообщения на веб-страницы или в магазины вне приложения. 
2.    Опросы — позволяют собирать информацию от конечных пользователей, задавая им вопросы.
3.    Отправка данных — позволяет вам отправлять файлы двоичных данных и файлы данных Base64. Информация, содержащаяся в отправленных данных отправляется в ваше приложение для изменения текущей работы пользователей в приложении. В вашем приложении должна быть возможность обработки данных в отправленных данных.
4.    Плитки (только в Windows Phone) — позволяют использовать службу push-уведомлений Майкрософт (MPNS) для отправки собственных push-уведомлений Windows с XML-данными. (Поддерживается, начиная с пакета SDK версии 0.9.0. Объем окончательных полезных данных в плитках не может превышать 32 килобайта.)

### Дополнительные материалы
-  [Основные понятия — глоссарий][Link 6]

## Три категории статистики в реальном времени, отображаемой для каждой кампании
1.    Push-уведомления — количество push-уведомлений, отправленных на основе критериев, заданных в кампании. 
2.    Ответы — количество пользователей, отреагировавших на уведомление, открыв его вне приложения или закрыв в приложении. 
3.    Действия — количество пользователей, щелкнувших ссылку в уведомлении для перенаправления в новое расположение в приложении, в хранилище или веб-браузер. 

> Примечание. Подробная статистика кампании доступна в API Reach в разделе «Статистика».

### Дополнительные материалы
-  [Основные понятия — глоссарий][Link 6], [Документация по API — API Reach — статистика][Link 4]


## Информация о кампании
Можно изменять, клонировать, удалять или активировать неактивированные кампании, наведя указатель мыши на их имена, или щелкнуть их, чтобы открыть. Можно клонировать уже активированные кампании, наведя указатель мыши на их имена, или щелкнуть их, чтобы открыть. Однако после активирования кампанию невозможно изменить.
 
![Reach1][18]

## Отклик на рекламную кампанию
В открытой активированной кампании можно переключаться между представлениями «Статистика» и «Подробная информация», а также между простым и расширенным представлением статистики, чтобы просмотреть дополнительную информацию (в зависимости от разрешений). Кроме того, информацию об отзывах и предложениях по рекламным кампаниям можно использовать как целевой критерий в новой кампании. Статистику по отзывам и предложениям по рекламным кампаниям также можно получить в разделе «Статистика» в Reach API. Кроме того, вы можете настроить аудиторию кампании push-уведомлений на основе предыдущих кампаний.


### Дополнительные материалы 
-  [Документация по пользовательскому интерфейсу — Reach — новая кампания push-уведомлений][27], [Документация по API — API Reach — статистика][Link 4].

![Reach2][19]

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

<!--HONumber=54--> 