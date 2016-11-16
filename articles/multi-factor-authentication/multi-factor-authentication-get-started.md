---
title: "Облачный и серверный варианты Azure MFA | Документация Майкрософт"
description: "Выберите подходящее решение многофакторной проверки подлинности, выяснив, что является объектом защиты и где находятся пользователи.  После этого выберите облако, сервер Multi-Factor Authentication или службы AD FS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 72347099d980f2ca73f39f984787197e1f87e45a


---
# <a name="choose-the-azure-multifactor-authentication-solution-for-you"></a>Выберите для себя решение "Многофакторная идентификация Azure".
Так как существует несколько вариантов использования многофакторной идентификации Azure (MFA), для определения наиболее подходящей версии следует ответить на некоторые вопросы.  Эти вопросы приведены ниже.

* [Что я пытаюсь защитить?](#what-am-i-trying-to-secure)
* [Где находятся пользователи?](#where-are-the-users-located)
* [Какие функции мне нужны?](#what-featured-do-i-need)

В разделах этой статьи представлены рекомендации, которые помогут найти ответы на эти вопросы.

## <a name="what-am-i-trying-to-secure"></a>Что я пытаюсь защитить
Чтобы выяснить, какой из вариантов двухфакторной проверки подлинности вам нужен, сначала определите, что вы пытаетесь защитить с помощью второго метода проверки подлинности.  Это приложение в службе Azure?  Или в системе удаленного доступа?  Определив объект, который необходимо защитить, мы будем знать, где именно нужно активировать многофакторную идентификацию.  

| Что вы пытаетесь защитить | Multi-Factor Authentication в облаке | Сервер Multi-Factor Authentication |
| --- |:---:|:---:|
| Приложения Майкрософт, получающие данные напрямую из источника |● |● |
| Приложения SaaS в коллекции приложений |● |● |
| Приложения IIS, опубликованные через прокси приложения Azure AD |● |● |
| Приложения IIS, опубликованные не через прокси приложения Azure AD | |● |
| Удаленный доступ, например VPN или RDG | |● |

## <a name="where-are-the-users-located"></a>Где находятся пользователи?
В зависимости от местонахождения пользователей можно определить, какое решение нам нужно для использования сервера MFA — облачное или локальное.

| Местонахождение пользователей | Multi-Factor Authentication в облаке | Сервер Multi-Factor Authentication |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD и локальная служба AD с использованием федерации в AD FS |● |● |
| Azure AD и локальная служба AD с использованием DirSync, Azure AD Sync, Azure AD Connect без синхронизации паролей |● |● |
| Azure AD и локальная служба AD с использованием DirSync, Azure AD Sync, Azure AD Connect с синхронизацией паролей |● | |
| Локальная служба Active Directory | |● |

## <a name="what-features-do-i-need"></a>Какие функции мне нужны?
В приведенной ниже таблице сравниваются возможности "Многофакторной идентификации" в облаке и на сервере "Многофакторной идентификации".

| Multi-Factor Authentication в облаке | Сервер Multi-Factor Authentication |
| --- |:---:|:---:|
| Уведомление от мобильного приложения в качестве второго фактора |● |
| Код подтверждения мобильного приложения в качестве второго фактора |● |
| Телефонный вызов в качестве второго фактора |● |
| Одностороннее SMS в качестве второго фактора |● |
| Двустороннее SMS в качестве второго фактора | |
| Маркеры оборудования в качестве второго фактора | |
| Пароли приложений для клиентов, не поддерживающих Multi-Factor Authentication |● |
| Администраторский контроль над методами проверки подлинности |● |
| Режим ПИН-кода | |
| Предупреждение о мошенничестве |● |
| Отчеты Multi-Factor Authentication |● |
| Разовый обход | |
| Настраиваемые приветствия для телефонных вызовов |● |
| Настройка идентификатора вызывающей стороны для телефонных звонков |● |
| Надежные IP-адреса |● |
| Запоминание данных MFA для доверенных устройств |● |
| Условный доступ |● |
| Кэш | |

Определившись с вариантом (облачная многофакторная проверка подлинности или локальный сервер Multi-Factor Authentication), можно начинать настройку и использование службы Azure Multi-Factor Authentication. **Выберите значок, соответствующий вашему сценарию.**

<center>




[![Облако](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Подтверждение](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>



<!--HONumber=Nov16_HO2-->


