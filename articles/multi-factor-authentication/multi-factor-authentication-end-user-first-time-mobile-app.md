.<properties 
	pageTitle="Использование мобильного приложения для связи при работе со службой Azure MFA" 
	description="На этой странице пользователи могут узнать, как использовать мобильное приложение в качестве основного способа связи при работе со службой Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="billmath"/>

# Использование мобильного приложения для связи при работе со службой Azure Multi-Factor Authentication

Из этой статьи вы узнаете, как использовать Microsoft Authenticator в качестве основного способа связи. Здесь описана настройка многофакторной проверки подлинности с использованием мобильного приложения в качестве основного способа связи.

Приложение Microsoft Authenticator доступно для платформ [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) и [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Использование Microsoft Authenticator в качестве способа связи


- На экране "Дополнительная проверка безопасности" из раскрывающегося списка выберите "Мобильное приложение".


![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- Выберите элемент «Уведомление» или «Одноразовый пароль» и нажмите кнопку «Установить».
- Запустите установленное на телефоне приложение Microsoft Authenticator и коснитесь "+", чтобы добавить новую учетную запись. Затем укажите, что вы хотите добавить рабочую или учебную учетную запись, после чего будет запущен сканер QR-кода. Если камера на телефоне неисправна, то можно вручную ввести сведения о компании. [Добавление учетной записи вручную](#adding-an-account-manually).

![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Отсканируйте изображение QR-кода на экране настройки мобильного приложения. Коснитесь "Готово", чтобы закрыть экран с QR-кодом.

![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Если не удается считать QR-код, то сведения можно ввести вручную.

![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- Когда на телефоне начнется процесс активации, нажмите кнопку Contact me (Связаться со мной). На ваш телефон будет отправлено уведомление или код проверки. Нажмите кнопку Verify (Проверить).

![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Некоторые компании для проверки могут потребовать ввести ПИН-код.

![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)


- Выполнив все необходимое, можно коснуться "Закрыть". На этом этапе проверка будет считаться выполненной.
- Теперь рекомендуется ввести номер своего мобильного телефона на случай утери доступа к мобильному приложению.
- Выберите соответствующую страну в раскрывающемся списке и введите номер мобильного телефона в соседнее поле. Нажмите кнопку Далее.
- На этом этапе способ связи будет настроен. Теперь нужно задать пароли приложений для внебраузерных приложений, таких как Outlook 2010 или более ранних версий. Если вы не используете такие приложения, щелкните **Готово**. В противном случае перейдите к следующему шагу.

![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Если вы используете такие приложения, скопируйте предоставленный пароль приложения и вставьте его во внебраузерное приложение. Действия для конкретных приложений, включая Outlook и Lync, см. в статьях «Изменение пароля электронной почты на пароль приложения» и «Изменение пароля приложения на пароль приложения».
- Нажмите кнопку Done (Готово).


## Добавление учетной записи вручную
Чтобы добавить учетную запись вручную, нажмите соответствующую кнопку.

![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

Теперь, если у вас есть учетная запись, для которой уже настроена Azure MFA, введите код и URL-адрес, указанные на той же странице, что и штрих-код. Данные необходимо ввести в поля кода и URL-адреса для мобильного приложения. После этого начнется активация.

![Настройка](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

После завершения активации нажмите кнопку Contact me (Связаться со мной). На ваш телефон будет отправлено уведомление или код проверки. Нажмите кнопку Verify (Проверить).



 

<!---HONumber=AcomDC_0831_2016-->