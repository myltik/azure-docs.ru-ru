<properties 
	pageTitle="" 
	description="Сведения о мастере начала работы с Azure Active Directory (проекты веб-API)." 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Приступая к работе](vs-active-directory-webapi-getting-started.md)
> - [Что произошло?](vs-active-directory-webapi-what-happened.md)


##Приступая к работе с Azure Active Directory (проекты веб-API)

#####Требование проверки подлинности для доступа к контроллерам
 
Ко всем контроллерам в проекте добавлен атрибут **Authorize**. Этот атрибут обеспечивает проверку подлинности пользователей перед их доступом к интерфейсам API, которые определяются этими контроллерам. Для анонимного доступа к контроллеру удалить с него этот атрибут. Если вы хотите задать разрешения на более детальном уровне, примените атрибут к каждому методу, требующему проверки подлинности, а не к классу контроллера.

[Дополнительные сведения о службе Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
