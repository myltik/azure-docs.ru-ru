---
title: "Условный доступ к Azure Active Directory: часто задаваемые вопросы | Документация Майкрософт"
description: "Часто задаваемые вопросы об условном доступе  "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: dee29df176389f39907d302cedc6143c0d3e3322
ms.lasthandoff: 04/12/2017


---
# <a name="azure-active-directory-conditional-access-faq"></a>Условный доступ к Azure Active Directory: часто задаваемые вопросы

## <a name="which-applications-work-with-conditional-access-policies"></a>Какие приложения работают с политиками условного доступа?

**Ответ.** См. раздел [Приложения и браузеры, использующие правила условного доступа в Azure Active Directory](active-directory-conditional-access-supported-apps.md).

---

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Являются ли политики условного доступа обязательными для службы совместной работы B2B и гостевых пользователей?
**Ответ.** Политики являются обязательными для пользователей службы совместной работы B2B. Тем не менее в некоторых случаях пользователь может не отвечать требованиям политики, например, если организация не поддерживает многофакторную проверку подлинности. В настоящее время политика не является обязательной для пользователей "Гость" SharePoint. Тип отношений "Гость" поддерживается внутри SharePoint. Политики доступа на сервер проверки подлинности не распространяются на гостевые учетные записи. В SharePoint гостевым доступом можно управлять.

---

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Распространяется ли политика SharePoint Online на OneDrive для бизнеса?
**Ответ.** Да.

---

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Почему не удается задать политику для клиентских приложений, таких как Word или Outlook?
**Ответ.** Политика условного доступа устанавливает требования для доступа к службе и является обязательной в случае проверки подлинности в этой службе. Политика не задается непосредственно в клиентском приложении, а применяется при вызове в службу. Например, политика, заданная в SharePoint, применяется к клиентам, вызывающим SharePoint, а политика, заданная в Exchange, применяется к Outlook.

--- 

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Распространяется ли политика условного доступа на учетные записи служб?
**Ответ.** Политика условного доступа распространяется на все учетные записи пользователей. Это относится и к учетным записям пользователей, которые используются как учетные записи служб. Во многих случаях учетная запись службы, которая выполняется автоматически, не отвечает требованиям политики. Например, это происходит, если требуется многофакторная проверка подлинности. В таких случаях учетные записи служб можно исключить из политики с помощью параметров управления политикой условного доступа. Дополнительные сведения о применении политики к пользователям читайте здесь.

---

## <a name="are-graph-apis-available-to-configure-configure-conditional-access-policies"></a>Доступны ли интерфейсы API Graph для настройки политик условного доступа?
**Ответ.** Пока нет. 

---
