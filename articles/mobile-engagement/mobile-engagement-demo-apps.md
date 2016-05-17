<properties
	pageTitle="Демонстрационное приложение Azure Mobile Engagement"
	description="Описывает, куда загружать, как использовать и как определять преимущества с помощью демонстрационного приложения Azure Mobile Engagement."
	services="mobile-engagement"
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="piyushjo" />

# Демонстрационное приложение Azure Mobile Engagement

Корпорация Майкрософт опубликовала демонстрационное приложение Azure Mobile Engagement для платформ **iOS**, **Android** и **Windows**, чтобы помочь вам найти полезные ресурсы и узнать больше об Azure Mobile Engagement.

Это приложение поможет вам:

1. Легко находить полезные ссылки на определенные ресурсы Azure Mobile Engagement, включая видео, документацию, форум, где можно создавать запросы новых функций, и т. д. 
2. Изучать примеры уведомлений, поддерживаемых Azure Mobile Engagement, для получения идей, которые можно использовать в собственных мобильных приложениях. 
3. Получить эталонную реализацию, которую можно использовать при изучения способов реализации Mobile Engagement в своем приложении для: 

	- сбора аналитических данных; 
	- реализации сложных сценариев уведомлений таких типов как *внутреннее полноэкранное представление* или *всплывающее окно*;
	- реализации опросов;
	- реализации сценариев использования автоматических push-уведомлений или отправки данных.   

## Установка приложения
Данное приложение доступно в соответствующих хранилищах приложений:

1. **Демонстрационное приложение Windows Universal**

	- [Ссылка на загрузку из Магазина Windows](https://www.microsoft.com/ru-RU/store/apps/azure-mobile-engagement/9nblggh4qmh2) 
	- Приложение было разработано как универсальное приложение для Windows 10, исходный код доступен на [Github](https://github.com/Azure/azure-mobile-engagement-app-ios).

2. **Демонстрационное приложение iOS**

	- [Ссылка на загрузку из Apple Store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090) 
	- Приложение было разработано в iOS Swift, исходный код доступен на [Github](https://github.com/Azure/azure-mobile-engagement-app-ios).

3. **Демонстрационное приложение Android**

	- [Ссылка на загрузку из магазина Google Play](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)
	- Исходный код доступен на [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![][1]

![][2] ![][3]


## Использование

Эти приложения можно использовать следующим образом:

**1) Загружать приложения на устройство по указанным выше ссылкам на магазин приложений.**

>[AZURE.IMPORTANT] Учетная запись Azure или подключение к какой-либо серверной части не требуется. Приложение будет работать независимо.

- Создав приложение на устройстве, вы сможете пройти по ссылкам в меню слева и найти все полезные ресурсы об Azure Mobile Engagement. 
- Мы также добавили в приложение [RSS-канал службы](https://aka.ms/azmerssfeed), чтобы у вас всегда был доступ к последним обновлениям.
- Кроме того, вы можете выполнить примеры сценариев уведомлений и проверить, какие типы уведомлений поддерживает Azure Mobile Engagement для каждой платформы. Эти уведомления можно получить локально, т. е. щелкнуть кнопки на экране и получить такие же уведомления, как при отправке уведомлений с платформы Azure Mobile Engagement. 

![][4]
    
![][5] ![][6]

**2) Загружать исходный код по указанным выше ссылкам в Github.**

- Загрузив исходный код, откройте его в соответствующей среде разработки, такой как XCode для iOS, Android Studio для Android и Visual Studio для Windows. 
- Затем необходимо выполнить [процедуру интеграции основного пакета SDK](mobile-engagement-windows-store-dotnet-get-started.md), чтобы подключить приложение к собственному экземпляру серверной части Mobile Engagement. 
	- Необходимо настроить строку подключения в приложении.  
	- Также необходимо настроить платформу push-уведомлений для приложения. 
- Вы заметите, что само приложение реализуется с использованием Azure Mobile Engagement, а значит, открыв приложение после его подключения к серверной части, вы увидите сеанс пользователя, его действия, события и т. д. на вкладке "Монитор". 
- Кроме того, вы сможете отправлять уведомления в это приложение из собственного экземпляра Mobile Engagement, не используя локальные уведомления. 
	- Здесь можно добавить устройство для тестирования, используя пункт меню **Получить идентификатор устройства** в приложении, который позволяет получить идентификатор устройства и зарегистрировать его как устройство для тестирования в собственном экземпляре серверной части платформы. 

	![][7]
	    
	![][8] ![][9]

## Основные функции демонстрационного приложения

1. Как уже говорилось, с этим приложением вы получаете все основные ресурсы Azure Mobile Engagement. Они доступны по ссылкам в меню слева. 

2. Взаимодействие с уведомлениями за пределами приложения для каждой платформы. Эти уведомления могут доставляться в режиме **только уведомления**, когда щелчок по уведомлению открывает только собственный экран приложения (используя **глубокую связь**), или **веб-объявление**, когда при щелчке по уведомлению отображается дополнительное HTML-содержимое из серверной части Mobile Engagement.

	![][29]

	
	- В iOS системные push-уведомления за пределами уведомлений можно увидеть, только закрыв приложение. Чтобы увидеть реализацию, добавьте **кнопки действий**, аналогичные кнопкам, добавленным в уведомление вне приложения (в данном случае *Отзыв* и *Поделиться*), чтобы пользователь мог выполнить действие прямо в уведомлении. 
	    
	![][11] ![][14]
	
	
	- В Android отображаются параметры, поддерживаемые Android в форме добавления многострочного текста (**большой фрагмент текста**) или изображения в уведомлении (**Общая картина**) вместе с **кнопками действий** как в iOS. 
	
	![][12] ![][15]
	
	
	- В Windows 10 уведомления выглядят как на ПК. Это уведомление будет также отображаться в **центре уведомлений** Windows 10. В настоящее время пакет SDK для Windows не поддерживает добавление **кнопок действий**. 
	
	![][10] ![][13]

3. Взаимодействие с уведомлениями в приложении по умолчанию для каждой платформы. Взаимодействие осуществляется в два этапа: сначала отображается окно **Уведомление**, при щелчке по которому открывается полноэкранное **объявление**, как показано ниже. Содержимое этого объявления берется из экземпляра серверной части Mobile Engagement. Пакет SDK включает шаблоны для уведомлений и объявлений, которые легко можно настроить как в демонстрационном приложении, добавив логотип и изменив цвета.

	![][16]
	
	![][17] **iOS** ![][18] **Android**

4. Для настройки взаимодействия по умолчанию можно также использовать функцию **Категория** в Azure Mobile Engagement. В демонстрационном приложении показаны два распространенных способа изменения взаимодействия с этими уведомлениями. Обратите внимание на то, что в пакете SDK для Windows эта функция пока не поддерживается.

	**Внутреннее полноэкранное представление**
	
	![][30]

	![][21] ![][22]

	**Всплывающее уведомление**

	![][31]
	
	![][19] ![][20]

5. Azure Mobile Engagement поддерживает также особый тип уведомлений в приложении, который называется **опросом** и позволяют отправлять быстрые опросы пользователям сегментированных приложений. Для каждого вопроса можно добавить вопросы и варианты ответов (как показано ниже), которые будут отображаться для пользователей приложения как уведомление в приложении.

	![][32]

	![][26]
	    
	![][27] **iOS** ![][28] **Android**

5. Azure Mobile Engagement поддерживает также передачу автоматических уведомлений об **отправке данных**, что позволяет передавать определенные данные из службы, например данные JSON как в приведенном ниже примере. Эти данные можно будет обработать в приложении и предпринять какие-то действия. Пример выборочного изменения цены на товар с использованием уведомления об отправке данных.

	![][33]

	![][23]
	    
	![][24] **iOS** ![][25] **Android**

> [AZURE.NOTE] Обратите внимание на то, что вы можете просмотреть пошаговые инструкции по любому из этих уведомлений, по ссылке *Щелкните здесь, что увидеть инструкции по отправке уведомлений с платформы Mobile Engagement* на любом экране с примером уведомления.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png

<!---HONumber=AcomDC_0504_2016-->