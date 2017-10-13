---
title: "Настройка политик условного доступа на основе устройств для Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как настроить политики условного доступа на основе устройств для Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: a57edd30975ec0e943fd84b2c66137d328a89b8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Настройка политик условного доступа на основе устройств для Azure Active Directory

Благодаря [условному доступу Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) можно настроить доступ авторизованных пользователей к вашим ресурсам. К примеру, вы можете ограничить доступ к определенным ресурсам доверенными устройствами. Политика условного доступа, требующая доверенного устройства, также называется политикой условного доступа на основе устройств.

В этом разделе предоставляются сведения о настройке политик условного доступа на основе устройств для приложений, подключенных к Azure AD. 


## <a name="before-you-begin"></a>Перед началом работы

Условный доступ на основе устройств объединяет **условный доступ Azure AD** и **управление устройствами Azure AD**. Если вы не знакомы с одной из этих областей, ознакомьтесь со следующими статьями:

- **[Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md)**. Здесь представлены общие сведения об условном доступе и связанная терминология.

- **[Introduction to device management in Azure Active Directory](device-management-introduction.md)** (Введение в управление устройствами в Azure Active Directory). Здесь представлен обзор различных вариантов подключения устройств в Azure AD. 


## <a name="trusted-devices"></a>Доверенные устройства

В эпоху мобильных и облачных технологий Azure Active Directory обеспечивает единый вход для устройств, приложений и служб из любого расположения. Но для некоторых ресурсов в вашей среде может быть недостаточно просто предоставить доступ соответствующим пользователям. Помимо этого для доступа к ресурсу может потребоваться доверенное устройство. В среде можно определить доверенное устройство, основываясь на следующих критериях:

- [Платформы устройств](active-directory-conditional-access-azure-portal.md#device-platforms) на устройстве.
- Является ли устройство совместимым.
- Подключено ли устройство к домену. 

[Платформы устройств](active-directory-conditional-access-azure-portal.md#device-platforms) характеризуются операционной системой, установленной на устройстве. В политике условного доступа на основе устройств можно ограничить доступ к определенным ресурсам определенными платформами устройств.



В пределах этой политики можно включить требование, в соответствии с которым доверенное устройство должно быть помечено как соответствующее требованиям.

![Облачные приложения](./media/active-directory-conditional-access-policy-connected-applications/24.png)

Это можно сделать в каталоге с помощью:

- Intune 
- сторонней управляемой системы для мобильных устройств, которая управляет устройствами c Windows 10 с помощью интеграции с Azure AD. 
 
  

Помечены как соответствующие требованиям могут быть только устройства, подключенные к Azure AD. Для подключения устройства к Azure Active Directory имеются следующие варианты: 

- регистрация в Azure AD;
- присоединение к Azure AD;
- присоединение к Azure AD (гибридные устройства).

    ![Облачные приложения](./media/active-directory-conditional-access-policy-connected-applications/26.png)

Если у вас имеется локальная конфигурация Active Directory (AD), следует назначить в качестве доверенных такие устройства, которые присоединены, а не подключены к Azure AD.

![Облачные приложения](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>Дальнейшие действия

Перед настройкой политики условного доступа на основе устройств в своей среде ознакомьтесь со статей [Рекомендации по работе с условным доступом в Azure Active Directory](active-directory-conditional-access-best-practices.md).

