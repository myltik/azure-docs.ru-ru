---
title: "Поиск неуправляемых облачных приложений с помощью Cloud App Discovery в Azure Active Directory | Документы Майкрософт"
description: "Содержит сведения о поиске приложений и управлении ими с помощью Cloud App Discovery, а также преимуществах и принципах работы Cloud App Discovery."
services: active-directory
keywords: "cloud app discovery, управление приложениями"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 35b898aa3c03aeef914a7df574ac65a22a6c7bec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Поиск неуправляемых облачных приложений с помощью Cloud App Discovery
## <a name="summary"></a>Сводка

Cloud App Discovery — это компонент Azure Active Directory Premium, который позволяет обнаруживать неуправляемые облачные приложения, используемые сотрудниками организации. На современных предприятиях ИТ-специалисты часто не знают обо всех облачных приложениях, при помощи которых пользователи выполняют свои задачи. Легко понять, почему администраторов беспокоят несанкционированный доступ к корпоративным данным, возможная утечка данных и другие угрозы безопасности. Эта недостаточная осведомленность может сильно усложнить разработку плана по борьбе с этими угрозами безопасности.

> [!TIP] 
> Познакомьтесь с улучшениями Cloud App Discovery в Azure Active Directory (Azure AD), которые были достигнуты за счет [интеграции с Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

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


## <a name="next-steps"></a>Дальнейшие действия
* [Вопросы безопасности и конфиденциальности Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Параметры реестра для настройки Cloud App Discovery для прокси-серверов с пользовательскими портами](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Журнал изменений агента Cloud App Discovery ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)

