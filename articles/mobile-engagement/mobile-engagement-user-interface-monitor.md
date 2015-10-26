<properties 
   pageTitle="Пользовательский интерфейс Azure Mobile Engagement: Monitor (Мониторинг)" 
   description="Наблюдение за работой приложения в режиме реального времени с помощью Azure Mobile Engagement" 
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

# Наблюдение за работой приложения в режиме реального времени
В разделе Monitor (Мониторинг) пользовательского интерфейса представлена диагностическая информация в режиме реального времени. Здесь вы можете настроить предупреждения о достижении пороговых значений для большинства параметров, исторические данные о которых доступны в разделе Analytics (Аналитика). В глоссарии в разделе «Понятия» приведены определения терминов и сокращений из разделов «Аналитика» и «Мониторинг», таких как активный пользователь, новый пользователь, сохраненный пользователь, сеанс, график маршрута пользователя, URL-адреса отслеживания, тренды, действие, событие, задание, ошибка, дополнительные сведения, сбой и сведения о приложении.

### Дополнительные материалы
-  [Понятия: глоссарий][Link 6], [Руководство по устранению неполадок: аналитика][Link 21]

## Мониторинг: сеансы, задания, события, ошибки и сбои
Вы можете контролировать количество пользователей, для которых в настоящий момент активны сеансы, а также число пользователей, у которых открыты определенные экраны и которые выполняют те или иные действия. Вы можете просматривать действия пользователей по сеансам, заданиям, событиям, ошибкам и сбоям. Вы можете отслеживать текущую информацию, а также просматривать сведения за прошлый час, день или неделю. Вы можете просмотреть все сведения в той или иной категории либо для определенного сеанса, задания, события, ошибки или сбоя. Средства мониторинга в режиме реального времени особенно полезны в период проведения определенных мероприятий, таких как кампания по отправке push-уведомлений, позволяя отследить рост активности пользователей после отправки им таких уведомлений.
 
![Монитор1][14]

## Устранение неполадок: раздел Monitor — Events — Details (Мониторинг — События — Сведения)
Определить идентификатор тестового устройства и убедиться в правильной интеграции средств Azure Mobile Engagement с инструментами аналитики, мониторинга и сегментации из своего приложения очень просто. Для этого нужно создать событие в приложении с тестового устройства, а затем найти его в разделе Monitor — Events — Details (Мониторинг — События — Сведения). Определив идентификатор тестового устройства, вы можете добавить его в список тестовых устройств в разделе My Account — Devices (Моя учетная запись — Устройства). Если вам не удается создать событие, убедитесь, что средства Azure Mobile Engagement правильно интегрированы с вашим приложением для платформы Android, iOS, Windows, Windows Phone или браузера с помощью SDK.

### Дополнительные материалы
-  [Документация к пакету SDK][Link 5]

![Монитор2][15]

## Устранение неполадок: раздел Monitor — Crashes — Details (Мониторинг — Сбои — Сведения)
Определить причины, по которым в приложении возникают сбои, можно с помощью информации о сбоях в разделе Monitor — Crashes — Details (Мониторинг — Сбои — Сведения). Также следует изучить сведения об известных проблемах и неполадках в различных версиях SDK в соответствующих примечаниях к выпуску пакета SDK для платформ Android, iOS, Windows, Windows Phone и браузера.

### Дополнительные материалы
-  [Документация к пакету SDK: примечания к выпуску][Link 5]

![Монитор3][16]

## Monitor — Alerts (Мониторинг — Предупреждения)
Вы также можете задать условия, при выполнении которых вам будет отправляться электронное письмо или мгновенное сообщение. (Поддерживаются все XMPP-совместимые службы, такие как Google Talk и Apple iChat.) Предупреждения генерируются при достижении заданного порога (сверху или снизу) по количеству сеансов, заданий, событий, ошибок или сбоев за секунду, минуту или час. Вы можете формировать предупреждения для любых действий того или иного типа либо отслеживать лишь определенное задание, событие или ошибку. Также можно указать минимальную частоту обнаружения — минимальный промежуток времени между двумя уведомлениями о предупреждении одного типа, чтобы при его срабатывании не получать более одного уведомления за определенный период.
 
![Монитор4][17]

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