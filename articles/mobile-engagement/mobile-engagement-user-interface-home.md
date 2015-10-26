<properties 
   pageTitle="Пользовательский интерфейс Azure Mobile Engagement — домашняя страница" 
   description="Узнайте, как управлять существующими приложениями и проектами с помощью Azure Mobile Engagement" 
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
   ms.date="08/10/2015"
   ms.author="piyushjo"/>

# Управление существующими приложениями и проектами
Раздел пользовательского интерфейса «Главная» содержит список всех приложений в разделе «Мои приложения», а также позволяет предоставлять разрешения для своих приложений другим пользователям. Создавая учетную запись, каждый пользователь получает доступ к домашней странице пользовательского интерфейса, но чтобы другие пользователи могли получать доступ к вашим настраиваемым приложениям в разделе «Мои проекты», нужно предоставить им соответствующие разрешения.

### Дополнительные материалы
-  [Руководство по устранению неполадок — служба][Link 24]

## Мои приложения
![Home1][2]

Этот краткий обзор приложений позволяет выбрать приложение, которое нужно открыть для подробного просмотра параметров ленты. Щелкните имя приложения, чтобы вернуться в последнее открытое расположение ленты в приложении, или значок с шестеренкой, чтобы перейти непосредственно на страницу «Параметры» вашего приложения. Вы можете выполнять поиск, фильтрацию или сортировку информации, которая отображается в таблицах приложений. Вы также можете перетаскивать заголовки столбцов, чтобы изменить их порядок.
 
В разделе, предоставляющем обзор приложений, содержится следующие элементы:

- «Всего пользователей» (общее количество пользователей);
- «Тенденции новых пользователей» (обзор новых пользователей за последние две недели);
- «Активные пользователи» (количество активных пользователей за последние 30 дней);
- «Тенденции активых пользователей» (обзор активных пользователей за последние две недели);
 
(Вы также можете просмотреть диаграмму сравнения приложений, выбрав следующее действие.)

- «Показать диаграмму сравнения» (может использоваться для отображения данных приложения в виде диаграммы);
- флажки (можно добавить или удалить приложение на/из диаграммы сравнения).
 
![Home2][3]

## Мои проекты
![Home5][6]

Вы можете использовать проекты, чтобы группировать приложения и предоставлять для них разрешения другим пользователям. Кнопка «Новые проекты» позволяет создать новый проект, для чего требуется только ввести значения в полях «имя» и «описание» для нового проекта. После создания вы можете щелкнуть имя проекта, чтобы изменить его имя и описание, а также выбрать все приложения, которые нужно включить в этот проект. Вы также можете удалить этот проект, при этом приложения, на которые он ссылается, удалены не будут. Тем не менее, вы потеряете доступ ко всем приложениям, которыми вы не владеете и которые не доступны из другого проекта. Поэтому будьте внимательны! Вы также можете приглашать других пользователей в проект, используя их электронные адреса.

### Существуют следующие роли.
- Наблюдатель: это пользователь, который может только просматривать приложения, связанные с проектом. Наблюдатель может получать доступ к данным аналитики и мониторинга, а также просматривать результаты рекламной кампании. Он не может менять какие-либо данные или управлять приложениями или пользователями. Наблюдатель не может создавать или менять состояние рекламной кампании.
- Разработчик: это пользователь, который может делать все то, что и наблюдатель, а также управлять приложениями. Он может включать и отключать приложения, менять их данные (такие как пакет и подпись) и создавать рекламные кампании. Однако разработчик не может управлять пользователями. 
- Администратор: это пользователь, который может делать все то, что и разработчик, а также управлять пользователями. Он может приглашать новых пользователей присоединиться к проекту, менять их роли и информацию о проекте. Для этой роли также можно задать разрешения на уровне приложения в меню «Параметры».
 
### Дополнительные материалы
-  [Документация по пользовательскому интерфейсу — параметры][Link 20]

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


 
 

<!---HONumber=Oct15_HO3-->