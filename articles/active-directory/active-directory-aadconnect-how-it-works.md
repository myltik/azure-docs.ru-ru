<properties 
	pageTitle="Принципы работы Azure AD Connect"
	description="Изучите принципы работы Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Принципы работы Azure AD Connect



Azure Active Directory Connect состоит из трех основных компонентов. Это службы синхронизации, службы федерации Active Directory (необязательный компонент) и компонент мониторинга, реализованный с помощью [Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- Синхронизация — эта часть состоит из компонентов и функций, ранее выпущенных как Dirsync и служба синхронизации AAD. Эта часть отвечает за создание пользователей и групп. Она также отвечает за согласование сведений о пользователях и группах в локальной среде и в облаке.
- AD FS — это необязательная часть Azure AD Connect, которая может использоваться для настройки гибридной среды с помощью локальной инфраструктуры AD FS. Ее можно использовать при сложном развертывании, в котором используется единый вход с присоединением к домену, принудительное применение политики входа AD и смарт-карты или сторонняя многофакторная проверка подлинности. Дополнительные сведения о настройке единого входа см. в разделе [DirSync с единым входом](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Мониторинг работоспособности — для сложных развертываний с использованием AD FS компонент Azure AD Connect Health реализует надежный мониторинг серверов федерации и представляет центральное расположение на портале Azure для просмотра связанных действий. Дополнительные сведения см. в разделе [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


## Вспомогательные компоненты Azure AD Connect

Ниже приведен перечень необходимых и вспомогательных компонентов, которые Azure AD Connect установит на сервере, где установлен Azure AD Connect. Этот список предназначен для базовой установки Express. Если вы решили использовать другой выпуск SQL Server на странице установки службы синхронизации, перечисленные ниже компоненты SQL Server 2012 не установлены.

- Соединитель Azure AD для Azure AD Connect
- Программы командной строки Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Модуль Azure Active Directory для Windows PowerShell
- Помощник по входу в Microsoft Online Services для ИТ-специалистов
- Распространяемый пакет Microsoft Visual C++ 2013




 

<!---HONumber=August15_HO9-->