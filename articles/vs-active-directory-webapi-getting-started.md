<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />
  
<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-active-directory-webapi-getting-started/)
> - [Что произошло?](/documentation/articles/vs-active-directory-webapi-what-happened/)


##Приступая к работе с Azure Active Directory (проекты веб-API)

#####Требование проверки подлинности для доступа к контроллерам
 
Ко всем контроллерам в проекте добавлен атрибут **Authorize**. Этот атрибут обеспечивает проверку подлинности пользователей перед их доступом к интерфейсам API, которые определяются этими контроллерам. Для анонимного доступа к контроллеру удалить с него этот атрибут. Если вы хотите задать разрешения на более детальном уровне, примените атрибут к каждому методу, требующему проверки подлинности, а не к классу контроллера.

[Дополнительные сведения о службе Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
