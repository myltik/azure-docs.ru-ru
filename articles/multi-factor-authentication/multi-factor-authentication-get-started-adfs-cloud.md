<properties 
	pageTitle="Защита облачных ресурсов с помощью Azure Multi-Factor Authentication и AD FS" 
	description="Эта страница посвящена Azure Multi-Factor Authentication. Она содержит сведения по началу работы с Azure MFA и AD FS в облаке." 
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
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Защита облачных ресурсов с помощью Azure Multi-Factor Authentication и AD FS

Если в организации настроена федерация с Azure Active Directory и имеются ресурсы, доступные для Azure AD, то, чтобы обеспечить безопасность этих ресурсов, вы можете использовать Azure Multi-Factor Authentication или службы федерации Active Directory. Чтобы защитить ресурсы Azure Active Directory с помощью Azure Multi-Factor Authentication или служб федерации Active Directory, следуйте инструкциям, приведенным ниже.

## Вот что нужно сделать, чтобы защитить ресурсы Azure AD с помощью AD FS 



1. Чтобы включить учетную запись, выполните действия, описанные здесь: [turn-on multi-factor authentication](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users).
2. Чтобы настроить правило утверждений, используйте такую процедуру:

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)</center>

- 	Запустите консоль управления AD FS.
- 	Перейдите к папке «Отношения доверия с проверяющей стороной» и щелкните нужное отношение правой кнопкой мыши. Выберите элемент «Изменить правила утверждения…».
- 	Нажмите кнопку «Добавить правило…».
- 	В раскрывающемся меню выберите пункт «Отправлять утверждения с помощью пользовательского правила» и нажмите кнопку «Далее».
- 	Введите имя для правила утверждений.
- 	Под заголовком «Пользовательское правило» добавьте приведенный ниже код.


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	Соответствующее утверждение.

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- Нажмите кнопку ОК. Нажмите кнопку Готово Закройте консоль управления AD FS.

После этого пользователи смогут выполнять вход с помощью локальных средств (например, с помощью смарт-карты).


 

<!---HONumber=July15_HO2-->