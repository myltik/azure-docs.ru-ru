---
title: "Назначение пользователей для личного домена в Azure Active Directory | Документация Майкрософт"
description: "Инструкции по заполнению пользовательского домена в Azure Active Directory учетными записями пользователей."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 717b5a7c-7bc3-4ab1-98b5-4740b53338fe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ccfa8d72b51c43e04d924d2b096e3cbc407af043
ms.contentlocale: ru-ru
ms.lasthandoff: 12/28/2016

---
# Назначение пользователей пользовательскому домену
<a id="assign-users-to-a-custom-domain" class="xliff"></a>
Добавив пользовательский домен в Azure Active Directory, вам нужно добавить учетные записи пользователей этого домена, чтобы затем начать проверку их подлинности.

## Пользователи, синхронизируемые из каталога в корпоративной сети
<a id="users-synced-in-from-a-directory-on-your-corporate-network" class="xliff"></a>
Если вы уже настроили связь между локальным каталогом Active Directory и Azure Active Directory, учетные записи будут добавлены во время синхронизации. Дополнительные сведения о синхронизации Azure Active Directory с локальным каталогом Active Directory см. в статье [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

## Пользователи добавляются и управляются в облаке
<a id="users-added-and-managed-in-the-cloud" class="xliff"></a>
Изменение домена для существующей учетной записи пользователя:

1. Откройте классический портал Azure под учетной записью глобального администратора или администратора пользователей.
2. Откройте свой каталог.
3. Перейдите на вкладку **Пользователи** .
4. Выберите пользователя из списка.
5. Измените домен для пользователя и выберите параметр **Сохранить**.

То же самое можно сделать с помощью [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) или [API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## Выбор пользовательского домена при создании пользователя
<a id="select-a-custom-domain-when-creating-a-new-user" class="xliff"></a>
1. Откройте классический портал Azure под учетной записью глобального администратора или администратора пользователей.
2. Откройте свой каталог.
3. Перейдите на вкладку **Пользователи** .
4. На панели команд нажмите **Добавить**.
5. При добавлении имени пользователя выберите пользовательский домен из списка доменов.
6. Щелкните **Сохранить**.

## Дальнейшие действия
<a id="next-steps" class="xliff"></a>
* [Использование пользовательских доменных имен для упрощения входа пользователей в систему](active-directory-add-domain.md)
* [Управление именами пользовательских доменов](active-directory-add-manage-domain-names.md)
* [Общие сведения об управлении доменами в Azure AD](active-directory-add-domain-concepts.md)


