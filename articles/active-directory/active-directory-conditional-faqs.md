---
title: 'Условный доступ к Azure Active Directory: часто задаваемые вопросы | Документация Майкрософт'
description: Ознакомьтесь с ответами на часто задаваемые вопросы об условном доступе к Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: ea93dab7df920f5a8ff8a1ae518b0bdbdcc80ae9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723819"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Условный доступ к Azure Active Directory: часто задаваемые вопросы

## <a name="which-applications-work-with-conditional-access-policies"></a>Какие приложения работают с политиками условного доступа?

Дополнительные сведения о приложениях, которые работают с политиками условного доступа, см. в статье [Приложения и браузеры, использующие правила условного доступа в Azure Active Directory](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Являются ли политики условного доступа обязательными для службы совместной работы B2B и гостевых пользователей?

Политики применяются для пользователей службы совместной работы B2B. Однако в некоторых случаях пользователь может не иметь возможности соответствовать требованиям политики. Например, организация гостевого пользователя может не поддерживать многофакторную проверку подлинности. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Распространяется ли политика SharePoint Online на OneDrive для бизнеса?

Да. Политика SharePoint Online также распространяется на OneDrive для бизнеса.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Почему не удается задать политику для клиентских приложений, таких как Word или Outlook?

Политика условного доступа устанавливает требования для доступа к службе. Она применяется, когда происходит проверка подлинности для этой службы. Политика не устанавливается напрямую в клиентском приложении. Вместо этого она применяется, когда клиент вызывает службу. Например, политика, заданная в SharePoint, применяется к клиентам, вызывающим SharePoint. Политика, заданная в Exchange, применяется к Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Распространяется ли политика условного доступа на учетные записи служб?

Политика условного доступа распространяется на все учетные записи пользователей. Это относится и к учетным записям пользователей, которые используются как учетные записи служб. Часто учетная запись службы, выполняемая автоматически, не может соответствовать требованиям политики условного доступа. Например, может потребоваться многофакторная проверка подлинности. Учетные записи служб можно исключить из политики с помощью параметров управления политикой условного доступа. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Доступны ли API Graph для настройки политики условного доступа?

В настоящее время нет. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Какова политика исключений по умолчанию для не поддерживаемых платформ?

В настоящее время политики условного доступа применяются избирательно к пользователям устройств iOS и Android. На приложения на других платформах эти политики по умолчанию не влияют. Администратор клиента может переопределить глобальную политику, чтобы запретить доступ пользователям на платформах, которые не поддерживаются.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Как работают политики условного доступа для Microsoft Teams?

Microsoft Teams в значительной степени зависит от Exchange Online и SharePoint Online в основных сценариях производительности, таких как собрания, календари и совместное использование файлов. Политики условного доступа, заданные для этих облачных приложений, применяются к Microsoft Teams при входе пользователя непосредственно в эту рабочую область.

Microsoft Teams также поддерживается отдельно в качестве облачного приложения в политиках условного доступа Azure Active Directory. Политики условного доступа, заданные для этих облачных приложений, применяются к Microsoft Teams при входе в систему пользователя. Однако без правильных политик для других приложений, таких как Exchange Online и SharePoint Online, пользователям по-прежнему может предоставляться к ним прямой доступ.

Клиенты рабочего стола Microsoft Teams для Windows и Mac поддерживают современные методы проверки подлинности. Современные методы проверки подлинности используют вход на основе библиотеки проверки подлинности Active Directory (ADAL) для клиентских приложений Microsoft Office на всех платформах.