<properties 
   pageTitle="Пользовательский интерфейс Azure Mobile Engagement — Рекламная кампания" 
   description="Узнайте, как создавать кампании push-уведомлений с помощью службы Azure Mobile Engagement, а также управлять ими" 
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


# Создание кампаний по рассылке push-уведомлений и управление ими
В разделе «Охват» можно создать новую кампанию push-уведомлений по сложной формуле, предоставив всю информацию, необходимую для отправки push-уведомлений. Параметры кампании по рассылке push-уведомлений немного отличаются в зависимости от типа кампании. Этих типов всего четыре: объявления, опросы, отправленные данные и плитки (только для Windows Phone).

### Параметр применяется к таким разделам:
- Языки: все типы кампаний (объявления, опросы, отправленные данные и плитки)
- Кампания: все типы кампаний (объявления, опросы, отправленные данные и плитки)
- Уведомление: объявления, опросы
- Содержимое: уникально для каждого типа кампании
- Аудитория: все типы кампаний (объявления, опросы, отправленные данные и плитки)
- Интервал времени: объявления, опросы и плитки
- Тест: все типы кампаний (объявления, опросы, отправленные данные и плитки)
 
![Кампания — охват1][20]

## Языки
С помощью раскрывающегося меню «Языки» можно отправлять разные версии push-уведомления на устройства, в которых настроено использование разных языков. По умолчанию все устройства будут получать одинаковые push-уведомления независимо от настроенного в них языка. Если в устройстве настроен другой язык, пользователь получит push-уведомление на языке по умолчанию. В кампании push-уведомлений есть множество параметров, позволяющих указать альтернативное содержимое для каждого из дополнительных выбираемых языков.
 
![Кампания — охват2][21]

### Различия в языке применяются к таким разделам:
- Языки: возможность выбора других дополнительных языков
- Кампания: одинаково для всех языков
- Уведомление: уникально для каждого дополнительно выбранного языка
- Содержимое: уникально для каждого дополнительно выбранного языка
- Аудитория: возможность фильтрации отдельно по языковому критерию
- Интервал времени: одинаково для всех языков
- Тест: возможность одновременной отправки для каждого языка
 
### Поддерживаемые языки:
- Арабский (ar) 
- Болгарский (bg) 
- Каталонский (ca) 
- Китайский (zh) 
- Хорватский (hr) 
- Чешский (cs) 
- Датский (da) 
- Нидерландский (nl) 
- Английский (en) 
- Финский (fi) 
- Французский (fr) 
- Немецкий (de) 
- Греческий (el) 
- Иврит (he) 
- Хинди (hi) 
- Венгерский (hu) 
- Индонезийский (id) 
- Итальянский (it) 
- Японский (ja) 
- Корейский (ko) 
- Латышский (lv) 
- Литовский (lt) 
- Малайский (макроязык) (ms) 
- Норвежский (букмол) (nb) 
- Польский (pl) 
- Португальский (pt) 
- Румынский (ro) 
- Русский (ru) 
- Сербский (sr) 
- Словацкий (sk) 
- Словенский (sl) 
- Испанский (es) 
- Шведский (sv) 
- Тагальский (tl) 
- Тайский (th) 
- Турецкий (tr) 
- Украинский (uk) 
- Вьетнамский (vi) 
 
## Кампания
Раздел «Охват» можно использовать, чтобы задать имя и категорию для кампании, а также, если вы не планируете использовать раздел «Аудитория» кампании push-уведомлений и хотите отправить ее через Reach API (и с помощью некоторых элементов API push-уведомлений низкого уровня). Категории можно использовать с пользовательским шаблоном уведомлений, чтобы управлять уведомлениями в приложении на основе предопределенных параметров. Список существующих категорий можно получить через API охвата.

> Предупреждение. Если в разделе «Кампания» рекламной кампании используется параметр «Игнорировать аудиторию, push-уведомления будут отправляться пользователям через API», кампания НЕ будет отправляться автоматически. Ее необходимо отправлять вручную через API рекламных кампаний.
 
![Кампания — охват3][22]
 
### Параметр применяется к таким разделам:
- Имя: все кампании
- Категория: объявления, опросы
- Игнорировать аудиторию, push-уведомление будет отправлено пользователям через API: все кампании
 
## Уведомление
В разделе «Уведомление» можно задать основные параметры для push-уведомлений. Эти параметры включают название push-уведомления, сообщение, изображения в приложении, а также возможность закрыть это уведомление. Многие параметры уведомлений зависят от платформы устройства. Вы можете выбрать способ отправки push-уведомлений: в приложении, за пределами приложения или оба варианта. (Помните, что пользователи могут согласиться или отказаться получать push-уведомления вне приложения на уровне операционной системы в устройствах. Azure Mobile Engagement не сможет переопределить этот параметр. Также следует помнить, что API охвата обрабатывает push-уведомления в приложении и вне приложения. Для обработки push-уведомлений вне приложения также используется API push-уведомлений). Для push-уведомлений можно настроить изображения или HTML-содержимое, включая прямые ссылки для перехода вне приложения или перехода в другое расположение в приложении (требуются категории намерений пакета SDK для Android версии 2.1.0 или более поздней). Вы можете изменить значок или индикатор iOS и отправлять текст или веб-содержимое (всплывающее окно с HTML-содержимым, ссылка с URL-адресом на другое расположение в или вне приложения). Кроме того, можно настроить устройство с ОС Android таким образом, чтобы оно издавало сигнал или вибрировало при получении push-уведомления. (Помните, чтобы устройство издавало сигнал или вибрировало, требуются правильные разрешения пакета SDK в файле манифеста в устройстве с ОС Android). Сейчас отраслевой стандарт для размера больших изображений устройств с ОС Android отсутствует, так как размеры экрана в каждом устройстве отличаются. Однако изображения размером 400x100 отображаются на экранах практически любого размера.

### Типы доставки
-    Только вне приложения: уведомление будет доставлено, когда пользователь не использует приложение.
- Для доставки уведомления только вне приложения требуется сертификат Apple или Google (сертификат APNS или GCM).
- Только в приложении: уведомление отображается, только если запущено приложение.
- Для доставки уведомления пользователю используется система доставки Capptain. Вы можете полностью настроить визуальный макет и отображение своего push-уведомления.
- В любое время: этот параметр отвечает за отправку уведомления независимо от того, работает приложение или нет.

 
![Кампания — охват4][23]

### Параметр применяется к таким разделам:
- Уведомление: объявления, опросы
 
## Содержимое
В разделе «Содержимое» можно изменить содержимое объявлений, опросов, отправленных данных и плиток (только для Windows Phone). Параметр, управляющий типом содержимого кампаний рush-уведомлений, зависит от типа кампании.

### Дополнительные материалы
- [Документация по пользовательскому интерфейсу — Рекламные кампании — Содержимое рush-уведомления][Link 29]
 
![Кампания — охват5][24]

## Аудитория
С помощью раздела «Аудитория» можно определить стандартный список элементов, ограничивающих кампании, или ограничить кампании на основе настраиваемых критериев. Стандартный набор параметров для ограничения аудитории позволяет отправлять push-уведомления старым и новым пользователям, а также отправлять пользователям только системные push-уведомления. Кроме того, можно задать квоту, чтобы ограничить число пользователей, которые получают push-уведомление. Вы можете вручную изменить выражение для фильтрации кампании, чтобы добавить один или несколько критериев для отслеживания пользователей. Выражение, определяющее связь между критериями, определяющими целевую аудиторию, можно вводить вручную. Такое выражение должно явно определять связь между критериями. Критерий описывается идентификатором, который должен начинаться с заглавной буквы и не должен содержать пробелы. Отношение между критериями можно описать с помощью операторов «and», «or», «not», а также символов «(",")». Пример: «критерий1 or (критерий1 and not критерий2)».

> Примечание. Если кампания содержит большую аудиторию, серверное сканирование таргетинга может выполняться медленно, особенно при попытке запуска нескольких кампаний одновременно.

- По возможности запускайте только одну кампанию за раз.
- Запускайте не больше четырех кампаний одновременно.
- Отправляйте push-уведомления только активным пользователям (флажок «Использовать только пользователей, которым можно отправить системное push-уведомление» и «Использовать только активных пользователей»), чтобы сканировались только пользователи с установленным приложением, использующие его. После определения аудитории можно нажать кнопку «Моделировать», чтобы узнать количество пользователей, которые получат это push-уведомление. Таким образом будет рассчитано количество известных пользователей, потенциально включенных в эту аудиторию (оценка основана на случайной выборке пользователей). Учтите, что пользователи, удалившие приложение, также являются частью этой аудитории, но им невозможно отправить уведомление.

### Дополнительные материалы
- [Документация по пользовательскому интерфейсу — Охват — Создание нового условия для push-уведомлений][Link 28]

![Кампания — охват6][25]

### Выражение для изменения
![Кампания — охват7][26]
 
### Параметр «Ограничить аудиторию» применяется к таким параметрам:
- Включать только подмножество пользователей: все типы кампании (объявления, опросы, отправленные данные, плитки)
- Включать только старых пользователей: все типы кампании (объявления, опросы, отправленные данные, плитки)
- Включать только новых пользователей: все типы кампании (объявления, опросы, отправленные данные, плитки)
- Включать только неактивных пользователей: объявления, опросы, плитки
- Включать только активных пользователей: все типы кампании (объявления, опросы, отправленные данные, плитки)
- Включать только пользователей, которым можно отправить системное push-уведомление: объявления, опросы
 
## Интервал времени
В разделе «Интервал времени» можно задать время отправки push-уведомления. Если оставить этот раздел пустым, кампания будет запущена немедленно. Помните, что при использовании часового пояса конечных пользователей кампания может запуститься на день раньше от ожидаемого времени запуска для конечных пользователей в Азии. При этом push-уведомления будут отправляться небольшими пакетами за раз, пока интервал времени во всех часовых поясах в мире не будет соответствовать интервалу времени, заданному для кампании. Кроме того, при использовании часового пояса конечных пользователей кампании также могут запускаться с задержкой, так как перед началом отправки push-уведомления кампании требуется запрашивать время с телефона.

> Примечание. Кампании без даты окончания могут кэшировать push-уведомления локально, отображая их даже после завершения кампании вручную. Чтобы избежать этого, указывайте время окончания для кампаний.

### Дополнительные материалы
- [Охват — Практические руководства — Планирование][Link 3] 
 
![Кампания — охват8][27]

### Возможности применения параметров
- Интервал времени: объявления, опросы и плитки
 
## Тест
С помощью раздела «Тест» перед сохранением кампании можно отправить push-уведомление на тестовое устройство. Если вы настроили какие-либо пользовательские языки для кампании, push-уведомление можно проверить для каждого из них. Тестовое устройство можно настроить в разделе «Моя учетная запись».
> Примечание. Если вы используете кнопку для тестирования push-уведомлений, данные на стороне сервера не записываются. Они записываются только для реальных кампаний push-уведомлений.

### Дополнительные материалы
- [Документация по пользовательскому интерфейсу — Моя учетная запись][Link 14]
 
![Кампания — охват9][28]

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
 

<!---HONumber=July15_HO4-->