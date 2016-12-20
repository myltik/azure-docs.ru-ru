---
title: "Начало работы с Azure Active Directory и подключенными службами Visual Studio (проекты WebApi) | Документация Майкрософт"
description: "Как начать использовать Azure Active Directory в проектах WebApi после подключения или создания Azure AD с помощью подключенных служб Visual Studio"
services: active-directory
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 05a507e328bb79e976ebccfdfae0dc2d7c82f374


---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>Начало работы с Azure Active Directory и подключенными службами Visual Studio (проекты WebApi)
> [!div class="op_single_selector"]
> * [Приступая к работе](vs-active-directory-webapi-getting-started.md)
> * [Что произошло?](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>Требование проверки подлинности для доступа к контроллерам
Ко всем контроллерам в проекте добавлен атрибут **Authorize** . Этот атрибут обеспечивает проверку подлинности пользователей перед их доступом к интерфейсам API, которые определяются этими контроллерам. Для анонимного доступа к контроллеру удалить с него этот атрибут. Если необходимо задать разрешения на более детальном уровне, примените атрибут к каждому методу, требующему проверки подлинности, а не к классу контроллера.

[Дополнительная информация о службе Azure Active Directory](https://azure.microsoft.com/services/active-directory/)




<!--HONumber=Nov16_HO3-->


