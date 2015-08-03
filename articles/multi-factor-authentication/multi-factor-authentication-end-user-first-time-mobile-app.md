<properties 
	pageTitle="Использование мобильного приложения для связи при работе со службой Azure MFA" 
	description="На этой странице пользователи могут узнать, как использовать мобильное приложение в качестве основного способа связи при работе со службой Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="billmath"/>

# Использование мобильного приложения для связи при работе со службой Azure Multi-Factor Authentication

В этой статье вы узнаете, как использовать мобильное приложение в качестве основного способа связи. Здесь описана настройка многофакторной проверки подлинности с использованием мобильного приложения в качестве основного способа связи.

Приложение Azure Authenticator доступно для [Windows Phone](http://www.windowsphone.com/ru-ru/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) и [iOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

## Использование мобильного приложения для связи
<ol>
<li>Из раскрывающегося списка выберите мобильное приложение.</li>

<center>![Установка](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)</center>

<li>Выберите элемент «Уведомление» или «Одноразовый пароль» и нажмите кнопку «Установить».</li>
<li>Запустите установленное на телефоне приложение Azure Authenticator и нажмите кнопку Scan barcode (Считать штрихкод).</li>


<center>![Установка](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)</center>


<li>Отсканируйте изображение штрихкода на экране настройки мобильного приложения. Щелкните «Готово», чтобы закрыть экран со штрихкодом. Если не удается считать штрихкод, сведения можно ввести вручную.</li>

<center>![Установка](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)</center>

<li>Когда на телефоне начнется процесс активации, нажмите кнопку Contact me (Связаться со мной). На ваш телефон будет отправлено уведомление или код проверки. Нажмите кнопку Verify (Проверить).</li>

<center>![Установка](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)</center>

<li>Нажмите кнопку Close (Закрыть). На этом этапе проверка будет считаться выполненной.</li>
<li>Теперь рекомендуется ввести номер своего мобильного телефона на случай утери доступа к мобильному приложению. <li>Выберите соответствующую страну в раскрывающемся списке и введите номер мобильного телефона в соседнее поле. Нажмите кнопку «Далее».</li> <li>На этом этапе способ связи будет настроен. Теперь нужно задать пароли приложений для внебраузерных приложений, таких как Outlook 2010 или более ранних версий. Если вы не используете такие приложения, щелкните **Готово**. В противном случае перейдите к следующему шагу.</li>

<center>![Установка](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)</center>

<li>Если вы используете такие приложения, скопируйте предоставленный пароль приложения и вставьте его во внебраузерное приложение. Действия для конкретных приложений, включая Outlook и Lync, см. в статьях «Изменение пароля электронной почты на пароль приложения» и «Изменение пароля приложения на пароль приложения».</li>
<li>Нажмите кнопку Done (Готово).</li>

<!---HONumber=July15_HO4-->