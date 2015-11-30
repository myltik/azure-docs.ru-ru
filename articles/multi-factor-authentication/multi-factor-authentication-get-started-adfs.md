<properties 
	pageTitle="Приступая к работе со службой Azure Multi-Factor Authentication и службами федерации Active Directory" 
	description="Эта страница содержит сведения о службе Azure Multi-Factor Authentication, описывающие начало работы с Azure MFA и AD FS." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="billmath"/>

# Приступая к работе со службой Azure Multi-Factor Authentication и службами федерации Active Directory



<center>![Облако](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Если в организации создана федерация локальной службы Active Directory со службой Azure Active Directory с помощью AD FS, доступны следующие два варианта использования Azure Multi-Factor Authentication.

- Защита облачных ресурсов с помощью службы Azure Multi-Factor Authentication и служб федерации Active Directory 
- Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication 

В следующей таблице перечислены возможности проверки подлинности для защиты ресурсов с помощью Azure Multi-Factor Authentication и AD FS.

|Проверка подлинности для браузерных приложений|Проверка подлинности для внебраузерных приложений
:------------- | :------------- | :------------- |
Защита ресурсов Azure AD с помощью Azure Multi-Factor Authentication |<li>Первый фактор проверки подлинности выполняется локально с помощью AD FS.</li> <li>Второй этап выполняется с использованием телефона для облачной проверки подлинности.</li>|Для входа пользователи могут использовать пароли приложений.
Защита ресурсов Azure AD с помощью служб федерации Active Directory |<li>Первый этап проверки подлинности выполняется локально с помощью AD FS.</li><li>Второй этап выполняется локально путем обработки утверждения.</li>|Для входа пользователи могут использовать пароли приложений.

Разъяснения касательно паролей приложений для федеративных пользователей.

- Пароль приложения проверяется с использованием облачной проверки подлинности и, следовательно, обходит федерации. Федерация активно используется только при настройке пароля приложения.
- Параметры контроля доступа локальных клиентов не учитываются при использовании пароля приложения.
- У вас нет возможности вести журнал локальной проверки подлинности для паролей приложений.
- Отключение или удаление учетной записи может занять до 3 часов с учетом синхронизации каталогов, которая задерживает процесс отключения или удаления пароля приложения в облачном удостоверении.

Дополнительные сведения о настройке службы Azure Multi-Factor Authentication или сервера Azure Multi-Factor Authentication с AD FS см. в таких статьях:

- [Защита облачных ресурсов с помощью службы Azure Multi-Factor Authentication и служб AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication и сервера Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication и AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)







 

<!---HONumber=Nov15_HO4-->