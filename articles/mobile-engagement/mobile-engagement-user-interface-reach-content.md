---
title: Пользовательский интерфейс Служб мобильного взаимодействия Azure — Содержимое рекламных кампаний
description: Информация об управлении уникальным контентом для различных типов кампаний push-уведомлений в Службах мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: add64f06-43c9-475c-8722-51cd00bb844b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 982cc66ffe98aa6dff8fe290cc1c2d4bad03c9ac
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Управление уникальным контентом для различных типов кампаний push-уведомлений
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

С помощью раздела «Содержимое» новой рекламной кампании можно изменять содержимое объявлений, опросов, отправок данных и плиток (только для Windows Phone). Параметр «Содержимое» кампаний push-уведомлений зависит от типа кампании. 

### <a name="content-types"></a>Типы содержимого
* Объявления
* Опросы
* Отправки данных
* Плитки (только для Windows Phone)

## <a name="content-of-announcements"></a>Содержимое объявлений
 ![Рекламная кампания — Содержимое 1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Выберите тип объявления:
* Только уведомление. Это простое стандартное уведомление. Это значит, что если пользователь щелкнет его, не отобразится никакое дополнительное представление, а будет только выполнено действие, связанное с ним.
* Текстовое объявление. Это уведомление, при появлении которого пользователь может посмотреть текстовое представление.
* Веб-объявление. Это уведомление, при появлении которого пользователь может посмотреть веб-представление.

### <a name="see-also"></a>См. также
* [Начало работы с push-уведомлениями и управление ими для взаимодействия с конечными пользователями][Link 3] 

### <a name="about-web-view-announcements"></a>Об объявлениях с веб-представлением
Шаблон {deviceid} в предоставляемом здесь HTML-коде или коде JavaScript будет автоматически заменен идентификатором устройства, в котором отображается объявление. Это простой способ получить идентификаторы устройств Служб мобильного взаимодействия Azure из внешней веб-службы, размещенной в операционном отделе организации.
Если вы хотите создать полноэкранное веб-представление (без предоставляемых кнопок по умолчанию «Действие» и «Выход»), вы можете использовать следующие функции в коде JavaScript объявления веб-представления: 

* выполнение действия объявления: ReachContent.actionContent()
* выход из объявления: ReachContent.exitContent()

### <a name="choose-your-action"></a>Выбор действия
### <a name="about-action-urls"></a>Об URL-адресах действия
Любой URL-адрес, который может интерпретироваться операционной системой целевого устройства, может использоваться как URL-адрес действия.
В качестве URL-адреса действия также может использоваться любой выделенный URL-адрес, поддерживаемый приложением (например, для перехода пользователя к определенному экрану).
Каждый шаблон {deviceid} автоматически заменяется идентификатором устройства, выполняющего действие. Таким способом можно легко получить идентификаторы устройств Служб мобильного взаимодействия Azure из внешней веб-службы, размещенной в операционном отделе организации.

* **Действия для Android и iOS**
  * Открытие веб-страницы
  * http://\[домен_веб_сайта\]; 
  * Пример:http://www.azure.com
  * Отправка сообщения электронной почты
  * mailto:\[получатель\]?subject=\[тема\]&body=\[сообщение\]; 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * Отправка SMS-сообщения
  * sms:\[номер_телефона\]; 
  * Пример: sms:2125551212
  * Набор номера телефона
  * tel:\[номер_телефона\]; 
  * Пример: tel:2125551212
* **Действия только для Android**
  * Скачивание приложения из Магазина Google Play
  * market://details?id=\[пакет_приложения\]; 
  * Пример: market://details?id=com.microsoft.office.word
  * Запуск поиска по географическому расположению
  * geo:0,0?q=\[запрос_поиска\]; 
  * Пример:geo:0,0?q=starbucks,paris
* **Действия только для iOS**
  * Скачивание приложения из магазина App Store
  * \http://itunes.apple.com/[страна]/app/[имя_приложения]/id[ИД_приложения]?mt=8 
  * Пример:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
  * Действия для Windows
  * Открытие веб-страницы
  * http://\[домен_веб_сайта\]; 
  * Пример:http://www.azure.com
  * Отправка сообщения электронной почты
  * mailto:\[получатель\]?subject=\[тема\]&body=\[сообщение\]; 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * Отправка SMS-сообщения (требуется приложение Skype из Магазина)
  * sms:\[номер_телефона\]; 
  * Пример: sms:2125551212
  * Набор номера телефона (требуется приложение Skype из Магазина)
  * tel:\[номер_телефона\]; 
  * Пример: tel:2125551212
  * Скачивание приложения из Магазина Google Play
  * ms-windows-store:PDP?PFN=\[идентификатор_пакета_приложения\]; 
  * Пример: ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
  * Запуск поиска по картам Bing
  * bingmaps:?q=\[запрос_поиска\]; 
  * Пример:bingmaps:?q=starbucks,paris
  * Использование пользовательской схемы
  * \[пользовательская_схема\]://\[параметры_пользовательской_схемы\]; 
  * Пример: myCustomProtocol://myCustomParams
  * Использование данных пакета (требуется приложение из Магазина для расширения для чтения)
  * \[папка\]\[данные\].\[расширение\]; 
  * Пример: myfolderdata.txt

### <a name="build-a-tracking-url"></a>Создание URL-адреса отслеживания
* См. указания по созданию URL-адреса отслеживания, который позволит пользователям скачивать одно из ваших приложений, в разделе «Параметры» в <UI Documentation>.

### <a name="define-the-texts-of-your-announcement"></a>Определение текста объявления
Введите заголовок, содержимое и названия для кнопок вашего объявления. Целевую аудиторию будущей кампании можно отслеживать на основе отзывов и предложений по рекламным кампаниям из ответов пользователей на эту кампанию. Кроме того, целевую аудиторию можно отслеживать на основе отзывов и предложений по действию, совершенному с кампанией (отправка push-уведомления, ответ, выполнение действия или выход).

### <a name="see-also"></a>См. также
* [Организация кампаний по рассылке push-уведомлений выбранному подмножеству пользователей с помощью условий таргетинга][Link 28]

## <a name="content-of-polls"></a>Содержимое опросов
![Рекламная кампания — Содержимое 2][31] 

Введите заголовок, описание и названия для кнопок вашего объявления. Затем добавьте вопросы и варианты ответов.
Целевую аудиторию будущей кампании можно отслеживать на основе отзывов и предложений по рекламным кампаниям из ответов пользователей на эту кампанию. Кроме того, целевую аудиторию можно отслеживать на основе действий, совершенных с кампанией (отправка push-уведомления, ответ, выполнение действия или выход). Кроме того, целевую аудиторию можно отслеживать на основе отзывов и предложений по ответам на опрос, в которых в качестве критериев используются вопрос и вариант ответа.

### <a name="see-also"></a>См. также
* [Организация кампаний по рассылке push-уведомлений выбранному подмножеству пользователей с помощью условий таргетинга][Link 28]

## <a name="content-of-data-pushes"></a>Содержимое отправки данных
![Рекламная кампания — Содержимое 3][32] 

### <a name="choose-the-type-of-your-data"></a>Выберите тип данных
* текст
* Двоичные данные
* Данные Base64

### <a name="define-the-content-of-your-data"></a>Определение содержимого данных
* Если вы выбрали отправку текстовых данных, скопируйте и вставьте текст в поле «Содержимое».
* Если вы выбрали отправку двоичных данных или данных base64, нажмите кнопку «Отправить файл» для отправки файла.
* Целевую аудиторию будущей кампании можно отслеживать на основе отзывов и предложений по рекламным кампаниям из ответов пользователей на эту кампанию. Кроме того, целевую аудиторию можно отслеживать на основе действий, совершенных с кампанией (отправка push-уведомления, ответ, выполнение действия или выход).

### <a name="see-also"></a>См. также
* [Организация кампаний по рассылке push-уведомлений выбранному подмножеству пользователей с помощью условий таргетинга][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Содержимое плиток (только для Windows Phone)
![Рекламная кампания — Содержимое 4][33]

### <a name="define-the-content-of-your-tile"></a>Определение содержимого плитки
Полезные данные плитки — это текст, который отображается на плитке приложения в устройствах Windows Phone.
Push-уведомление плитки представляет собой версию службы push-уведомлений Майкрософт (MPNS) для Windows Phone. Push-уведомление плитки — это единственный тип push-уведомлений без ответа, и поэтому аудиторию будущих кампаний нельзя создать на основе результатов кампании push-уведомлений плитки. 

### <a name="see-also"></a>См. также
* [Mobile Engagement][Link 4]

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

