<properties 
	pageTitle="Почему для настройки средства Azure AD Connect нужна учетная запись глобального администратора Azure AD" 
	description="Описание причин, по которым при использовании пользовательских параметров нужна учетная запись глобального администратора." 
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

# Почему для настройки средства Azure AD Connect нужна учетная запись глобального администратора Azure AD

В следующей таблице показано, почему для настройки службы Azure AD Connect нужна учетная запись глобального администратора Azure AD.

Условие | Описание 
------------- | ------------- |
Стандартные параметры и обновление DirSync | Мы включаем синхронизацию (при необходимости) в вашем каталоге Azure AD и создаем учетную запись Azure AD, которая будет использоваться для операций непрерывной синхронизации (учетная запись соединителя Azure AD). 
Пользовательские параметры | Мы включаем синхронизацию в каталоге Azure AD и создаем учетную запись Azure AD, которая будет использоваться для операций непрерывной синхронизации (учетная запись соединителя Azure AD). Для единого входа с возможностью использования AD FS мы считываем и настраиваем свойства федерации в Azure AD.



**Дополнительные ресурсы**


* [Дополнительные сведения об учетных записях и разрешениях Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect на портале MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=July15_HO4-->