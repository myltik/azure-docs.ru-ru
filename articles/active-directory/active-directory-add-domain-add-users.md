---
title: "Назначение пользователей для личного домена в Azure Active Directory | Документация Майкрософт"
description: "Инструкции по заполнению пользовательского домена в Azure Active Directory учетными записями пользователей."
services: active-directory
documentationcenter: 
author: jeffgilb
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
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 39cb54a6637088c35c6aef864a804c24803f48ba
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---
# <a name="assign-users-to-a-custom-domain"></a>Назначение пользователей пользовательскому домену
Добавив пользовательский домен в Azure Active Directory, вам нужно добавить учетные записи пользователей этого домена, чтобы затем начать проверку их подлинности.

> [!IMPORTANT]
> Для управления службой Azure AD мы рекомендуем использовать [Центр администрирования Azure AD](https://aad.portal.azure.com) на портале Azure, а не классический портал Azure, который упоминается в этой статье. Сведения об управлении доменными именами в центре администрирования Azure AD см. в статье [Управление личными доменными именами в Azure Active Directory](active-directory-domains-manage-azure-portal.md).

## <a name="users-synced-from-a-on-premises-directory"></a>Пользователи, синхронизированные из локального каталога
Если вы уже настроили связь между локальным каталогом Active Directory и Azure Active Directory, учетные записи будут добавлены во время синхронизации. Дополнительные сведения о синхронизации Azure Active Directory с локальным каталогом Active Directory см. в статье [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

## <a name="users-added-and-managed-in-the-cloud"></a>Пользователи добавляются и управляются в облаке
Изменение домена для существующей учетной записи пользователя:

1. Откройте классический портал Azure под учетной записью глобального администратора или администратора пользователей.
2. Откройте свой каталог.
3. Перейдите на вкладку **Пользователи** .
4. Выберите пользователя из списка.
5. Измените домен для пользователя и выберите параметр **Сохранить**.

То же самое можно сделать с помощью [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) или [API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>Выбор пользовательского домена при создании пользователя
1. Откройте классический портал Azure под учетной записью глобального администратора или администратора пользователей.
2. Откройте свой каталог.
3. Перейдите на вкладку **Пользователи** .
4. На панели команд нажмите **Добавить**.
5. При добавлении имени пользователя выберите пользовательский домен из списка доменов.
6. Щелкните **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия
* [Использование пользовательских доменных имен для упрощения входа пользователей в систему](active-directory-add-domain.md)
* [Управление именами пользовательских доменов](active-directory-add-manage-domain-names.md)
* [Общие сведения об управлении доменами в Azure AD](active-directory-add-domain-concepts.md)


