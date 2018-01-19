---
title: "Поиск неуправляемых облачных приложений с помощью Cloud App Discovery в Azure Active Directory | Документы Майкрософт"
description: "Содержит сведения о поиске приложений и управлении ими с помощью Cloud App Discovery, а также преимуществах и принципах работы Cloud App Discovery."
services: active-directory
keywords: "cloud app discovery, управление приложениями"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Поиск неуправляемых облачных приложений с помощью Cloud App Discovery
## <a name="summary"></a>Сводка

Cloud App Discovery в Azure Active Directory теперь предоставляет возможность расширенного обнаружения без агента на платформе Microsoft Cloud App Security. Чтобы использовать Cloud App Discovery, просто войдите в систему с помощью Azure AD Premium P1. Это обновление доступно для всех клиентов Azure AD Premium P1 без дополнительной платы. Сначала ознакомьтесь со статьей [Настройка Cloud App Discovery в Azure AD](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started), затем попробуйте поработать с [Microsoft Cloud App Security](https://portal.cloudappsecurity.com/).

> [!IMPORTANT] 
> Текущий интерфейс Azure AD Cloud App Discovery с обнаружением на основе агента планируется отключить 5 марта 2018 г. После этого агенты отключатся, а данные будут удалены. Примите необходимые меры до 5 марта, чтобы приступить к работе в новом интерфейсом и избежать перебоев в работе службы.  
 
**С помощью Cloud App Discovery вы сможете:**

* Обнаруживать используемые облачные приложения и оценивать их использование по числу пользователей, объему трафика или числу веб-запросов к приложению.
* Идентифицировать пользователей, использующих приложение.
* Экспортировать данные для автономного анализа.
* Получить контроль над этими приложениями с помощью средств ИТ и включить единый вход для управления пользователями.

## <a name="how-it-works"></a>Принцип работы
1. На компьютерах пользователей устанавливаются агенты использования приложения.
2. Информация об использовании приложения, записанная агентами, отправляется по безопасному зашифрованному каналу в службу Cloud App Discovery.
3. Служба Cloud App Discovery оценивает данные и создает отчеты.

![Схема Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>Дополнительная информация
* [Вопросы безопасности и конфиденциальности Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Параметры реестра для настройки Cloud App Discovery для прокси-серверов с пользовательскими портами](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Журнал изменений агента Cloud App Discovery ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)

