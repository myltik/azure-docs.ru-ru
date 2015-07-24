<properties 
	pageTitle="Использование смайликов эмодзи в push-уведомлениях" 
	description="Как использовать смайлики эмодзи в push-уведомлениях"					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="piyushjo" />

#Использование смайликов эмодзи в push-уведомлениях

Вы можете включать в ваши push-уведомления смайлики эмодзи. В настоящее время для текстовых уведомлений «вне приложения» в Azure Mobile Engagement поддерживается только набор трехбайтных смайликов эмодзи. Выполните следующие действия.

1.  Сначала найдите библиотеку трехбайтных смайликов эмодзи. Все смайлики эмодзи доступны по этой [ссылке](http://stackoverflow.com/questions/10153529/emoji-on-mysql-and-php-why-some-symbol-yes-other-not).

	![][1]

2. Перейдите на вкладку «Охват» на портале Azure Mobile Engagement.

3. Выберите тип push-уведомления (объявление, опрос и т. д.). В данном примере мы выберем объявление.

4. Заполняйте поля уведомления, пока не дойдете до основного текста. В это поле можно добавить смайлик эмодзи. Вы можете вставить его как в заголовок, так и в основной текст.

	![][2]

5. Перейдите по указанной выше ссылке и вырежьте смайлик эмодзи, который хотите использовать. Вставьте его в любое место заголовка или основного текста сообщения.

6. Заполните остальные поля уведомления и сохраните его.

7. При тестировании или активации объявления появится уведомление с выбранным вами смайликом.

	![][3] 
	![][4]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/emoji.png
[2]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/notification_android.png
[4]: ./media/mobile-engagement-use-emoji-with-push/notification_ios.png
 

<!----HONumber=July15_HO2-->