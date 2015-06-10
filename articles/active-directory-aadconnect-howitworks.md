<properties 
	pageTitle="Принципы работы Azure AD Connect" 
	description="Изучите принципы работы Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Принципы работы Azure AD Connect

Azure Active Directory Connect состоит из трех основных частей. Это службы синхронизации, необязательный компонент служб федерации Active Directory и компонент мониторинга, реализованный с помощью Azure AD Connect Health.


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-howitworks/AADConnectStack.png) </center>

- Синхронизация — эта часть состоит из компонентов и функций, ранее выпущенных как Dirsync и служба синхронизации AAD. Эта часть отвечает за создание пользователей и групп. Она также отвечает за согласование сведений о пользователях и группах в локальной среде и информации в облаке.
- AD FS — это необязательная частью Azure AD Connect, которая может использоваться для настройки гибридной среды с помощью локальной инфраструктуры AD FS. Эта часть может использоваться для работы со сложными развертываниями, в которых используется единый вход с присоединением к домену, принудительное применение политики входа AD и смарт-карточки или сторонняя многофакторная проверка подлинности. Дополнительные сведения о настройке единого входа см. в разделе [DirSync с единым входом](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Мониторинг работоспособности — для сложных развертываний с использованием AD FS компонент Azure AD Connect Health реализует надежный мониторинг серверов федерации и представляет центральное расположение на портале Azure для просмотра связанных действий. Дополнительные сведения см. в разделе [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).






**Дополнительные ресурсы**

* [Использование локальной инфраструктуры удостоверений в облаке](active-directory-aadconnect.md)
* [Приступая к работе с Azure AD Connect](active-directory-aadconnect-getstarted.md)
* [Управление Azure AD Connect](active-directory-aadconnect-manage.md)
* [Azure AD Connect на портале MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->