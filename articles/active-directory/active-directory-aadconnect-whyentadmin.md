<properties 
	pageTitle="Для чего нужна учетная запись администратора предприятия" 
	description="Описание пользовательских параметров." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Почему при настройке Azure AD Connect необходимо указывать учетную запись администратора предприятия для подключения к AD DS

В следующей таблице показано, почему для настройки Azure AD Connect нужна учетная запись администратора предприятия.

Условие | Описание 
------------- | ------------- |
Стандартные параметры и обновление DirSync | <li>Для стандартных параметров мы создаем локальную учетную запись службы Active Directory, которая используется для синхронизации (также называется учетной записью соединителя AD), и назначаем соответствующие разрешения для синхронизации и синхронизации паролей.</li> <li>Для обновления Dirsync мы сбрасываем пароль в текущей учетной записи соединителя AD и настраиваем новую службу синхронизации Azure AD Connect для использования этой учетной записи. </li>



**Дополнительные ресурсы**


* [Дополнительные сведения об учетных записях и разрешениях Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect на портале MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=July15_HO3-->