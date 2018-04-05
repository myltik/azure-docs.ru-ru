---
title: Пользовательский интерфейс Служб мобильного взаимодействия Azure — «Аналитика»
description: Научитесь анализировать журналы данных о приложении с помощью Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6b2533ac-b8ec-4e35-872c-d563895bdc0c
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fceae1ffff40fc525170121181e21726fe2bd3f7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-analyze-historical-data-about-your-application"></a>Анализ журналов данных о приложении
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

В этой статье описывается вкладка **Аналитика** портала **Служб мобильного взаимодействия**. Портал **Служб мобильного взаимодействия** используется для отслеживания мобильных приложений и управления ими. Обратите внимание, что для того, чтобы начать пользоваться порталом, сначала необходимо создать учетную запись **Служб мобильного взаимодействия Azure**.

Раздел «Аналитика» пользовательского интерфейса предоставляет сводные данные о приложении на основе данных журналов, которые обновляются каждые 24 часа. Информация отображается на разных панелях мониторинга, состоящих из графиков, линейчатых и круговых диаграмм, сеток и карт. Данные также можно скачивать в виде CSV-файлов. Большая часть этой информации доступна в реальном времени в разделе «Монитор» пользовательского интерфейса, и к ней также можно получить через Analytics API.

> [!NOTE]
> Многие части интерфейса портала **Служб мобильного взаимодействия** содержат кнопку **Показать справку**. Нажмите эту кнопку, чтобы получить больше контекстной информации по соответствующему разделу.

## <a name="standard-and-custom-analytics"></a>Стандартные и пользовательские аналитические данные
Службы мобильного взаимодействия Azure предоставляют набор основных и стандартных аналитических данных о приложениях. Вы сможете представлять эти данные графически, как только интегрируете свое приложение, используя пакет SDK. Службы мобильного взаимодействия Azure также предоставляют возможность собирать дополнительные пользовательские аналитические данные о поведении конечных пользователей. Для этого вы можете создать план тегов — настраиваемых тегов информации о приложении Tags (app info) в разделе **Параметры**, чтобы Службы мобильного взаимодействия Azure могли собирать такие дополнительные данные.

## <a name="analytics"></a>Аналитика
* «Панель мониторинга»: показывает общую информацию о новых и активных пользователях, а также их тенденции.
* «Пользователи»: пользователи идентифицируются по идентификаторам своих устройств: этот идентификатор уникален для каждого устройства (каждый новый пользователь будет фактически учитываться как одно новое устройство). Пользователь считается новым в течение заданного интервала времени, если он провел свой первый сеанс во время указанного интервала. Пользователь считается удержанным, если он провел по крайней мере один сеанс в течение последних 7 дней. Активные пользователи — это пользователи, которые провели по крайней мере по одному сеансу в течение заданного периода. Периоды времени можно сортировать по ежемесячному, еженедельному, ежедневному или ежечасовому периоду. Все диаграммы похожи, но их данные можно фильтровать по различным категориям, таким как версия приложения, а затем по периоду времени. При интеграции пакета SDK собираются следующие стандартные данные: об активных пользователях, новых пользователях, количестве сеансов, времени каждого сеанса, технической информации о стране, локальных переменных, расположении, языке, операторе, устройствах, встроенном ПО, сети (Wi-Fi), а также версии приложения и пакете SDK, используемых клиентами. Эту информацию можно просмотреть в режиме реального времени в разделе мониторинга.

> [!NOTE]
> Период времени основан на дате, заданной в параметрах устройства пользователя, поэтому, если на телефоне пользователя установлена неправильная дата, он может зайти в систему в неправильный период времени.

* Удержание: пользователь считается удержанным в течение заданного интервала времени, если он провел свой первый сеанс во время указанного интервала. Вы можете изменить интервалы времени, в течение которых учитываются удержанные (и новые) пользователи, чтобы вести учет в часах, днях, неделях или месяцах. Аналитика удержания пользователей построена на основе контингентов. Контингент — это набор новых пользователей, обнаруженных в течение заданного периода (т. е. набор пользователей, которые провели свой первый сеанс в течение этого периода времени). Мы используем контингенты таких периодов, как 1 день, 2 дня, 4 дня, 7 дней или 1 месяц. Если для контингента задан период — каждый (1) день, каждые 2 дня, 4 дня, 7 дней или каждый (1) месяц, Службы мобильного взаимодействия Azure вычисляют набор всех активных пользователей, которые принадлежат к этой группе (т. е. набор пользователей, которые провели по крайней мере один сеанс во время периода). Этот набор пользователей называется версией контингента. (В Службах мобильного взаимодействия Azure можно увидеть, сколько пользователей по-прежнему используют ваше приложение, но только в магазине определенной платформы можно узнать, сколько пользователей установило ваше приложение, например в GooglePlay, iTunes, Магазине Windows и др.)
* «Сеансы»: сеансом называется одно использование приложения пользователем. Сеансы формируются из последовательности действий, выполняемых пользователями (действие обычно связано с использованием одного экрана приложения, но ситуация может меняться в зависимости от способа, с помощью которого в приложение интегрирован пакет SDK). Пользователь может выполнять только одно действие за раз: сеанс запускается, как только пользователь запускает первое действие, и завершается, когда пользователь завершает последнее действие. Если пользователь не выполняет никаких действий несколько секунд, последовательность действий разбивается на два отдельных сеанса.
* «Действия»: имя каждого экрана в приложении и продолжительность времени, на протяжении которого пользователь использует каждый экран. Действия представляют собой пользовательские аналитические параметры, которые соответствуют тегам «информация о приложении», настроенным вами для приложения:
* "Путь пользователя": показывает, как пользователи переходят по действиям в приложении (экранам). Вы можете переместить ползунок, чтобы настроить уровень детализации. Синие узлы означают действия в приложении. Их размер пропорционален времени, затраченному пользователями на выполнение. Белые узлы означают запуск и завершение сеанса. Красные узлы означают сбои. Связи означают переходы между действиями в приложении (или от действий к сбою). Щелкните узел или связь, чтобы отобразить подсказку с дополнительной информацией о данных: времени, проведенном на том или ином экране, количестве переходов и проценте перехода исходного действия в действие назначения. (A---60 %--->Б означает, что на переход от действия А к действию Б пользователь затратил 60 % времени.) Если граф необходимо сделать более точным, вы можете реорганизовать его. Его положение сохраняется при каждом изменении. Вы можете также отобразить или скрыть сбои, чтобы немного очистить граф.
* «События»: определенные действия, выполненные пользователем в приложении. Распределение событий отображается как число событий на каждого пользователя за сеанс. Событие представляет собой мгновенное действие, например нажатие кнопки или получение уведомления. (Значение событий зависит от того, как пакет SDK интегрирован в приложение.) События могут возникать во время сеанса или выполнения задания, а также происходить автономно.
* «Задания»: аналогичны событиям, но учитывают продолжительность действия. Например, задания могут сообщать техническую информацию о том, сколько времени занимает загрузка содержимого или вызов веб-службы. Они также могут показывать, сколько времени затрачивает пользователь на заполнение формы, создание учетной записи или покупку. Задание представляет собой длительность выполнения задачи, например, длительность выполнения задачи скачивания или время, на протяжении которого баннер отображается на экране. (Значение заданий зависит от того, как пакет SDK интегрирован в приложение.) Обычно задания связаны с фоновыми задачами, которые выполняются за пределами области сеанса (т. е. без действий пользователя).
* «Техническая информация»: техническая информация об устройствах пользователей приложения, которую можно отслеживать, например информация о языковом стандарте, операторе, сети, устройстве, встроенном ПО, размерах экранов устройств пользователей, а также о версии приложения и версии пакета SDK, используемого в нем.
* «Ошибки»: информация о технических ошибках в приложении, которые не приводят к его сбою. Ошибка представляет собой проблему мгновенного характера, например сбой сети или неправильное выполнение операции. (Значение событий зависит от того, как пакет SDK интегрирован в приложение.) Ошибки могут возникать во время сеанса или выполнения задания, а также происходить автономно.
* «Сбои»: информация об ошибках, из-за которых произошел сбой приложения. Сбой — это непредвиденное условие, при котором приложение перестает выполнять ожидаемые от него функции и должно остановить работу. Обычно сбой происходит по причине ошибки в приложении.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Доступ к обзору периода удержания
![Analytics3][12]

Обзор периода удержания разбит по середине на несколько карт, на каждой из которых отображается обзор определенного периода удержания. В качестве примера показан период удержания продолжительностью 2 дня. На других картах показаны периоды удержания продолжительностью 4 дня и 7 дней.

## <a name="understanding-the-retention-overview-cards"></a>Общая информация о картах обзора периода удержания
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Каждая карта состоит из трех основных частей:
1. 1: учтенные контингент и период.
2. 2–4: показатель удержания для текущего периода.
3. 5: спарклайн предыдущих значений.

### <a name="here-is-detailed-information-about-each-element"></a>Ниже приведена подробная информация по каждому элементу:
1. «Контингент и период»: в этом заголовке указан тип контингента. Здесь указан «2-дневный период». Это означает, что мы рассмотрим поведение пользователей за последние 2 дня, в том числе новых пользователей, которые присоединились в течение последних 2 дней, и обратим внимание на то, будут ли они подключаться в течение следующих 2 дней. В приведенном выше примере показана активность пользователей между 21 и 22 ноября.
2. Этот элемент предоставляет коэффициент удержания в период между 21 и 22 ноября для пользователей, которые зарегистрировались 19 и 20 ноября. Среди трех новых пользователей, зарегистрированных 19 и 20 ноября, в период между 21 и 22 ноября был один активный пользователь.
3. Этот индикатор отображает вышеупомянутую информацию графически. (Треть круга соответствует 33 %.) Каждый цвет предоставляет дополнительную информацию: зеленый цвет означает, что после предыдущего вычисления количество увеличилось, желтый — что количество не изменилось, а красный — что количество снизилось.
4. Этот элемент отображает значения, используемые для вычисления.
5. Это спарклайн предыдущих значений удержания. Он позволяет просмотреть предыдущие значения, чтобы получить широкое представление о тенденции развития.

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
