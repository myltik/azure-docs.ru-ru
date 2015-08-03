<properties 
   pageTitle="Пользовательский интерфейс Azure Mobile Engagement - Настройки" 
   description="Узнайте, как управлять глобальными параметрами приложения с помощью Azure Mobile Engagement" 
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

# Управление глобальными параметрами приложения
Параметры в меню «Параметры», доступные для приложения, отличаются в зависимости от платформы приложения и разрешений, которые вы ему предоставили. Вот что включает в себя меню «Параметры»: «Подробности», «Проекты», «Системные push-уведомления», «Скорость отправки push-уведомлений», «Пакет SDK», «Отслеживание», «Информация о приложении», «Коммерческое давление» и «Разрешения». Только пункт меню «Информация о приложении» в разделе «Параметры» пользовательского интерфейса содержит элементы, которыми можно управлять с помощью функции «Тег» в API устройства. «Глоссарий» в разделе «Основные понятия» содержит следующие определения терминов и сокращений: APNS, GCM, IDFA, API, пакет SDK, ключ API, ключ SDK, идентификатор приложения, идентификатор AppStore, план добавления тегов, идентификатор пользователя, идентификатор устройства, делегат приложения, трассировка стека и создание глубинных ссылок.

### Дополнительные материалы
- [Документация по API — API устройств][Link 4], [Основные понятия — глоссарий][Link 6], [Руководство по устранению неполадок — служба][Link 24]

  ![settings1][46]

## Сведения
Позволяет менять имя и описание приложения, а также просматривать его владельца и разрешения, назначенные вашей роли. Область «Конфигурация аналитики»: позволяет просматривать и менять день, с которого начинается неделя, и время удержания в днях.
 
  ![settings2][47]
 
## Проекты
Позволяет выбрать все проекты, которые необходимо отображать в приложении. Вы также можете найти проект и просмотреть имя, описание, владельца и разрешения своей роли для любого проекта, в который входит ваше приложение.

### Дополнительные материалы
-    [Документация по пользовательскому интерфейсу — домашняя страница][Link 13]
 
  ![settings3][48]

## Системные push-уведомления
Позволяет удалить существующий сертификат или зарегистрировать новый сертификат, который необходимо использовать при передаче системных push-уведомлений. Системные push-уведомления позволяют Azure Mobile Engagement отправлять push-уведомления в ваше приложение в любое время, даже если оно не работает. Указав учетные данные или сертификаты по крайней мере в одной службе системных push-уведомлений, вы можете выбрать «Всегда» при создании рекламных кампаний, а также использовать параметр «Уведомитель» в API push-уведомлений.

### Дополнительные материалы
- [Документация по API — API Reach][Link 4], [Документация по API — API Push][Link 4], [Документация по пользовательскому интерфейсу — рекламные кампании — новая кампания push-уведомлений][Link 27]

### Служба push-уведомлений Apple (APNS)
Чтобы включить системные push-уведомления, используя службу push-уведомлений Apple, вам необходимо зарегистрировать свой сертификат. Необходимо указать тип сертификата — сертификат разработки или производственный сертификат. Затем будет необходимо передать сертификат и ввести пароль.

### Дополнительные материалы
- [Документация по пакету SDK — iOS — Подготовка приложения для push-уведомлений Apple][Link 5]
 
![settings4][49]
 
### Служба push-уведомлений Windows (WPNS)
Чтобы включить системные push-уведомления, используя службу уведомлений Windows, необходимо указать учетные данные приложения. Вам понадобятся ваши идентификатор безопасности пакета (SID) и секретный ключ.
 
![settings5][50]
 
### Google Cloud Messaging (GCM) для Android
Чтобы включить системные push-уведомления с помощью GCM, необходимо следовать указаниям, предоставленным Google. Затем необходимо вставить простой ключ API сервера, настроенный без ограничений IP-адресов. Требуется интеграция пакета SDK для Android версии v1.12.0 или более поздней.

### Дополнительные материалы
- [Документации по пакету SDK — Android — интеграция GCM][Link 5], [Разработчики Google — руководство по GCM](http://developer.android.com/guide/google/gcm/gs.html), [Разработчики Google — документация по GCM](http://developer.android.com/google/gcm/index.html)
 
### Amazon Device Messaging (ADM) для Android
Чтобы включить системные push-уведомления, используя ADM, необходимо указать Amazon <OAuth credentials>, которые состоят из идентификатора клиента и секрета клиента (требуется интеграция с пакетом SDK для Android версии 2.1.0 или более поздней).

### Дополнительные материалы
- [Документация по пакету SDK — Android — Как интегрировать ADM][Link 5], [Разработчики Amazon Developer — документация по ADM](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## Скорость отправки push-уведомлений
Показывает текущую скорость отправки push-уведомлений и позволяет определить скорость их передачи для приложения. Скорость отправки push-уведомлений определяет максимальное количество push-уведомлений в секунду, которое отправит модуль обработки рекламных кампаний. Это может быть полезным в ситуации, когда собственные серверы перегружены из-за слишком большого количества запросов в секунду (или несколько секунд) после активации кампании.
 
  ![settings7][52]

## Отслеживание
Функция отслеживания позволяет отслеживать источники установки приложений iOS и Android. Она позволяет узнать, откуда пользователи скачали приложение (т. е. из какого магазина приложений) и из какого источника они узнали о нем (например, из рекламной кампании, блога, веб-сайта, сообщения, SMS и т. д.). Функцию отслеживания Azure Mobile Engagement необходимо интегрировать в приложение с помощью пакета SDK, сделав это как отдельный шаг.

### Дополнительные материалы
- [Документация по пакету SDK — Android — Как интегрировать][Link 5], [Документация по пакету SDK — iOS — Как интегрировать][Link 5]
 
### Магазины
Позволяет регистрировать все магазины, из которых можно скачать ваше приложение, используя их имена и связанные с ними URL-адреса скачивания. При этом вы можете создавать расположения для размещения URL-адресов отслеживания. На этой странице можно создавать магазины и удалять их с нее. Щелкнув значок для создания URL-адреса отслеживания, вы перейдете на страницу URL-адресов, показанную ниже. Существует несколько способов отслеживания того, откуда пользователи скачивают ваше приложение:

-    использование стороннего рекламного сервера (сейчас Azure Mobile Engagement поддерживает [SmartAd](http://smartadserver.fr/) и [Surikate](http://www.surikate.com/));
-    использование сторонней службы атрибуции (сейчас Azure Mobile Engagement поддерживает [Mobile App Tracking](http://www.mobileapptracking.com/), в ближайшем будущем планируется поддержка [Trademob](http://www.trademob.com/));
-    использование стороннего источника ссылок на установку (сейчас Azure Mobile Engagement поддерживает [Google Play Install Referrer](https://developers.google.com/app-conversion-tracking/docs/third-party-trackers/), но только для Android);
-    создание отчетов вручную.
 
  ![settings8][53]
 
### Рекламные кампании —
позволяет создавать рекламные кампании, состоящие из имени рекламного сервера, кода кампании и источника, из которого можно скачать приложение.
 
  ![settings9][54]
 
### URL-адреса отслеживания —
позволяет создавать URL-адреса отслеживания, чтобы использовать их в качестве целевых URL-адресов в источниках (рекламных кампаниях, блогах, на веб-сайтах, в сообщениях, SMS и т. д.), которые могут перенаправлять пользователей в магазины, в которых можно скачать ваше приложение. Кроме этого, вы также можете отобразить все URL-адреса отслеживания, которые вы уже создали. URL-адрес отслеживания можно использовать как URL-адрес действия рекламной кампании или объявления о рекламной кампании, чтобы предложить пользователям скачать одно из ваших приложений из другого приложения. URL-адрес отслеживания перенаправляет по URL-адресу скачивания, связанному с выбранным магазином, и позволяет нашей системе отслеживания записывать данные о магазине, из которого скачано приложение, во время установки. Если указан источник, наша система отслеживания также записывает данные о нем, позволяя различать разные рекламные кампании, созданные для вашего приложения.

При создании URL-адреса отслеживания необходимо указать магазин и выбрать значение источника «Нет», «Пользовательский» или «Рекламный сервер».

-    Если выбрать значение источника «Нет», будет создан URL-адрес отслеживания по умолчанию.
-    Если выбрать значение источника «Пользовательский», для скачивания приложения можно указать URL-адрес на внешнем сервере.
-    Если выбрать в качестве источника рекламный сервер, будет создан URL-адрес отслеживания по умолчанию на рекламном сервере с именем по умолчанию.
 
### Дополнительные материалы
- [Создание кампании по рассылке push-уведомлений и управление ею][Link 27] 
 
### Создание URL-адреса отслеживания
Можно также создать URL-адрес отслеживания, который позволяет пользователям скачать одно из ваших приложений в разделе содержимого новой рекламной кампании.

### Дополнительные материалы
- [Документация по пользовательскому интерфейсу — Рекламные кампании — Содержимое рush-уведомления][Link 29]

![settings10][55]

## Информация о приложении
Позволяет зарегистрировать дополнительную информацию, связанную с пользователями вашего приложения. Эту информацию можно вставить с помощью вашего приложения (используя пакет SDK) или сервера (используя API устройства).

### Дополнительные материалы
- [Документация по API — API устройств][Link 4]

Регистрация тегов информации о приложении позволяет сегментировать рекламные кампании, создавая критерии для определенных аудиторий на основе сегментов. Вы можете просматривать имена и типы тегов информации о приложении для существующего приложения или добавлять новую информацию о приложении на основе имени и типа строки, даты, целого числа или логического значения.

### Дополнительные материалы
- [Создание кампании по рассылке push-уведомлений и управление ею][Link 27]
 
![settings11][56]
 
## Коммерческое давление
Квоты на отправку push-уведомлений позволяют определить максимальное количество раз, когда на устройство можно отправить push-уведомление, за определенный период. Квоты на отправку push-уведомлений используются в рекламных кампаниях с включенным параметром «Применять квоты на отправку push-уведомлений». Квотами на отправку push-уведомлений можно управлять по умолчанию или с помощью сегмента. Для квот можно задавать отправку только максимального количества push-уведомлений за такой скользящий период, как за последний час, день, неделю или месяц.

### Дополнительные материалы
- [Документация по пользовательскому интерфейсу — рекламные кампании — новая кампания push-уведомлений][Link 27], [Документация по API — сегменты][Link 18]

### Квоты:
- Квота по умолчанию. Эта квота применяется к пользователям, которые не соответствуют ни одному сегменту в разделе «Квота по сегменту» ниже. По умолчанию квота не задана.
- Квота по сегменту. Так как квоту необходимо применять к группе пользователей, вам нужно создать сегмент для идентифицикации пользователей, к которым следует ее применять. Это может быть сегмент активных пользователей, определяемый по количеству проведенных ими сеансов, или какой-нибудь другой сегмент, связанный с интересующей вас особенностью группы пользователей. Если устройство соответствует нескольким сегментам с заданной квотой, применяется только квота с самым большим количеством отправляемых push-уведомлений в час.

![settings12][57]
 
## Разрешения
Позволяет искать и просматривать данные об адресе электронной почты, имени, организации и уровне разрешений пользователей вашего приложения. Понятие разрешений — это дополнение к роли в проекте. Оно позволяет связать один набор разрешений с определенным пользователем, у которого есть доступ к приложению.

### Сейчас доступны такие уровни разрешений:
-    «Создатель рекламной кампании» (пользователь, который может создавать рекламные кампании); 
-    «Менеджер рекламной кампании» (пользователь, который может создавать рекламные кампании, а также активировать, приостанавливать, завершать и удалять их).

> Примечание. Если у пользователя есть роль в проекте и набор разрешений для данного приложения, используется наиболее разрешительный подход. Именно поэтому мы советуем в случае использования разрешений присваивать своим пользователям роль «Наблюдатель» (наименее разрешительную роль) и добавлять более разрешительные разрешения на уровне приложения.

### Дополнительные материалы
- [Документация по пользовательскому интерфейсу — домашняя страница][Link 13]  
 
![settings13][58]

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
 

<!---HONumber=July15_HO4-->