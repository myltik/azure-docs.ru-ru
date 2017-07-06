---
title: "Включение защиты идентификации Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как включить защиту идентификации Azure Active Directory."
services: active-directory
keywords: "защита удостоверений Azure Active Directory, Cloud App Discovery, управление приложениями, безопасность, риск, уровень риска, уязвимость, политика безопасности"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f7a7ffaf-76bf-4cc7-96a1-86c944275c82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: f231252c222c5520d0a66f9b40749bd203545f16
ms.openlocfilehash: 44ee1e58b65b23eeb3fa3710fcb29a1646d5f3ad
ms.contentlocale: ru-ru
ms.lasthandoff: 01/12/2017


---
# <a name="enabling-azure-active-directory-identity-protection"></a>Включение защиты идентификации Azure Active Directory
Защита идентификации Azure Active Directory — это новая возможность, которая обеспечивает единое представление подозрительных операций входа в систему и потенциальных уязвимостей. В этой службе реализованы уведомления, рекомендации по исправлению и политики на основе рисков, помогающие защитить организацию. 

Данная служба обнаруживает подозрительные операции входа в систему по пользовательским и привилегированным (администраторы) удостоверениям по таким признакам, как атаки методом подбора, раскрытые учетные данные, попытки входа в систему из неизвестных расположений и зараженные устройства, чтобы обеспечить защиту в реальном времени. Что более важно, на основе этих подозрительных операций вычисляется серьезность риска пользователя, и можно настроить политики на основе рисков, чтобы обеспечить автоматическую защиту удостоверений своей организации. Дополнительные сведения см. в статье [Защита идентификации Azure Active Directory](active-directory-identityprotection.md).

В этом разделе показано, как включить защиту идентификации Azure Active Directory.

## <a name="steps-to-enable-azure-active-directory-identity-protection"></a>Инструкции по включению защиты идентификации Azure Active Directory
1. [Войдите](https://ms.portal.azure.com/) на портал Azure с правами глобального администратора. 
2. На портале Azure щелкните **Marketplace**.
   
    ![Создание](./media/active-directory-identityprotection-enable/01.png "Создание")
3. В списке приложений щелкните **Безопасность+идентификация**.
   
    ![Создание](./media/active-directory-identityprotection-enable/02.png "Создание")
4. Щелкните **Azure AD Identity Protection**(Защита идентификации Azure AD).
   
    ![Создание](./media/active-directory-identityprotection-enable/03.png "Создание")
5. В колонке **Azure AD Identity Protection** (Защита идентификации Azure AD) щелкните **Создать**.
   
    ![Создание](./media/active-directory-identityprotection-enable/04.png "Создание")

## <a name="next-steps"></a>Дальнейшие действия
* [Защита идентификации Azure Active Directory.](active-directory-identityprotection.md)


