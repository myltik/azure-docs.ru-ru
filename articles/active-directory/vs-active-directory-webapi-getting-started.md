<properties 
	pageTitle="Начало работы с Azure Active Directory и подключенными службами Visual Studio (проекты WebApi) | Microsoft Azure" 
	description="Как начать использовать Azure Active Directory в проектах WebApi после подключения или создания Azure AD с помощью подключенных служб Visual Studio" 
  services="active-directory"
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/18/2015" 
	ms.author="tarcher"/>

# Начало работы с Azure Active Directory и подключенными службами Visual Studio (проекты WebApi)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##Требование проверки подлинности для доступа к контроллерам
 
Ко всем контроллерам в проекте добавлен атрибут **Authorize**. Этот атрибут обеспечивает проверку подлинности пользователей перед их доступом к интерфейсам API, которые определяются этими контроллерам. Для анонимного доступа к контроллеру удалить с него этот атрибут. Если необходимо задать разрешения на более детальном уровне, примените атрибут к каждому методу, требующему проверки подлинности, а не к классу контроллера.

[Дополнительная информация о службе Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=AcomDC_0128_2016-->