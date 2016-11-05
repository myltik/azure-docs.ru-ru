---
title: Начало работы с Azure Active Directory и подключенными службами Visual Studio (проекты WebApi) | Microsoft Docs
description: Как начать использовать Azure Active Directory в проектах WebApi после подключения или создания Azure AD с помощью подключенных служб Visual Studio
services: active-directory
documentationcenter: ''
author: TomArcher
manager: douge
editor: ''

ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: tarcher

---
# Начало работы с Azure Active Directory и подключенными службами Visual Studio (проекты WebApi)
> [!div class="op_single_selector"]
> * [Приступая к работе](vs-active-directory-webapi-getting-started.md)
> * [Что произошло?](vs-active-directory-webapi-what-happened.md)
> 
> 

## Требование проверки подлинности для доступа к контроллерам
Ко всем контроллерам в проекте добавлен атрибут **Authorize**. Этот атрибут обеспечивает проверку подлинности пользователей перед их доступом к интерфейсам API, которые определяются этими контроллерам. Для анонимного доступа к контроллеру удалить с него этот атрибут. Если необходимо задать разрешения на более детальном уровне, примените атрибут к каждому методу, требующему проверки подлинности, а не к классу контроллера.

[Дополнительная информация о службе Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

<!---HONumber=AcomDC_0817_2016-->