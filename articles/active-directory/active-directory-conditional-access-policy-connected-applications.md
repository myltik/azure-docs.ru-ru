---
title: Настройка политик условного доступа на основе устройств для Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить политики условного доступа на основе устройств для Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 07957d5ec843c414813d69b7084915bcd70a5a61
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930863"
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Настройка политик условного доступа на основе устройств для Azure Active Directory

С помощью [условного доступа Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) можно управлять доступом авторизованных пользователей к ресурсам. Например, вы можете сделать так, чтобы доступ к определенным ресурсам имели только управляемые устройства. Политика условного доступа, которая требует наличия доверенного устройства, также называется политикой условного доступа на основе устройств.

В этом разделе описано, как настроить политики условного доступа на основе устройств для подключаемых приложений Azure AD. 


## <a name="before-you-begin"></a>Перед началом работы

Условный доступ на основе устройств объединяет **условный доступ Azure AD** и **управление устройствами Azure AD**. Если вы не знакомы с одной из этих областей, ознакомьтесь со следующими статьями:

- **[Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md)**. Здесь представлены общие сведения об условном доступе и связанная терминология.

- **[Introduction to device management in Azure Active Directory](device-management-introduction.md)** (Введение в управление устройствами в Azure Active Directory). Здесь представлен обзор различных вариантов подключения устройств в Azure AD. 



## <a name="managed-devices"></a>Управляемые устройства  

В эпоху мобильных и облачных технологий Azure Active Directory обеспечивает единый вход для устройств, приложений и служб из любого расположения. Но для некоторых ресурсов в вашей среде может быть недостаточно просто предоставить доступ соответствующим пользователям. Наряду с выбором пользователей вам также может потребоваться, чтобы доступ был возможен только с управляемого устройства.

Управляемое устройство — это устройство, которое соответствует вашим стандартам безопасности и нормативным требованиям. Проще говоря, управляемые устройства — это устройства, которые находится под *некоторым* контролем организации. В Azure AD чтобы устройство стало управляемым, его необходимо зарегистрировать в Azure AD. При регистрации устройства создается удостоверение устройства в виде объекта устройства. Azure использует этот объект для отслеживания сведений о состоянии устройства. Как администратор Azure AD вы уже могли использовать этот объект для переключения (включения или отключения) состояния устройства.
  
![Состояния на основе устройства](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Существует три варианта регистрации устройств в Azure AD:

- **[Зарегистрированные устройства Azure AD](device-management-introduction.md#azure-ad-registered-devices)**  — регистрация личного устройства в Azure AD

- **[Устройства, присоединенные к Azure AD](device-management-introduction.md#azure-ad-joined-devices)** — получение устройства Windows 10, которое принадлежит организации и не присоединено к локальному AD, зарегистрированному в Azure AD. 

- **[Гибридные устройства, присоединенные к Azure AD](device-management-introduction.md#hybrid-azure-ad-joined-devices)** — получение устройства Windows 10, которое присоединено к локальному AD, зарегистрированному в Azure AD.

В качестве управляемых устройств можно использовать зарегистрированные устройства с гибридным присоединением к Azure AD или устройства, которые были помечены как соответствующие.  

![Состояния на основе устройства](./media/active-directory-conditional-access-policy-connected-applications/47.png)


 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Требовать устройства с гибридным присоединением к Azure AD

В политике условного доступа можно выбрать **Требовать устройство с гибридным присоединением к Azure AD**, чтобы указать, что доступ к выбранным облачным приложениям может осуществляться только с управляемых устройств. 

![Состояния на основе устройства](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Этот параметр применяется только к устройствам Windows 10, которые присоединены к локальному Azure AD. Эти устройства можно зарегистрировать в Azure AD только с помощью гибридного присоединения к Azure AD. Это [автоматизированный процесс](device-management-hybrid-azuread-joined-devices-setup.md) для регистрации устройств Windows 10. 

![Состояния на основе устройства](./media/active-directory-conditional-access-policy-connected-applications/45.png)

Что делает устройство с гибридным присоединением к Azure AD управляемым устройством?  Для устройств, присоединенных к локальному AD, предполагается, что для управления этими устройствами используются такие решения, как **System Center Configuration Manager (SCCM)** или **групповая политика**. Так как Azure AD не может определить, был ли применен к устройству любой из этих методов, обязательное использование устройства с гибридным присоединением к Azure AD считается относительно слабым механизмом использования управляемых устройств. Вы как администратор должны решить, являются ли методы, применяемые к локальным устройствам, присоединенным к домену, достаточно надежными для определения управляемого устройства, если такое устройство также является гибридным устройством, присоединенным к Azure AD.


## <a name="require-device-to-be-marked-as-compliant"></a>Требовать, чтобы устройство было отмечено как соответствующее

Вариант *Требовать, чтобы устройство было отмечено как соответствующее* является самым надежным методом определения управляемого устройства.

![Состояния на основе устройства](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Для использования этого варианта устройство должно быть зарегистрировано в Azure AD, а также помечено как совместимое с помощью:
         
- Intune 
- сторонней управляемой системы для мобильных устройств, которая управляет устройствами c Windows 10 с помощью интеграции с Azure AD. 
 
![Состояния на основе устройства](./media/active-directory-conditional-access-policy-connected-applications/46.png)



Для устройства, которое помечено как совместимое, можно предположить, что: 

- Мобильные устройства, которые ваши сотрудники используют для доступа к данным организации, являются управляемыми
- Мобильные приложения, которые используют ваши сотрудники, являются управляемыми
- Сведения о вашей организации защищены благодаря контролю над тем, как ваши сотрудники обращаются к этим данным и обмениваются ими
- Устройство и приложения на нем соответствуют требованиям безопасности организации




## <a name="next-steps"></a>Дополнительная информация

Перед настройкой политики условного доступа на основе устройств в своей среде ознакомьтесь со статей [Рекомендации по работе с условным доступом в Azure Active Directory](active-directory-conditional-access-best-practices.md).

