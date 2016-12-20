---
title: "Включение защиты идентификации Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как включить защиту идентификации Azure Active Directory."
services: active-directory
keywords: "защита удостоверений Azure Active Directory, Cloud App Discovery, управление приложениями, безопасность, риск, уровень риска, уязвимость, политика безопасности"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f7a7ffaf-76bf-4cc7-96a1-86c944275c82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7321e73bf30e8cba5a7a61a6705d285617f70122


---
# <a name="enabling-azure-active-directory-identity-protection"></a>Включение защиты идентификации Azure Active Directory
Защита идентификации Azure Active Directory — это новая возможность, которая обеспечивает единое представление подозрительных операций входа в систему и потенциальных уязвимостей. В этой службе реализованы уведомления, рекомендации по исправлению и политики на основе рисков, помогающие защитить организацию. 

Данная служба обнаруживает подозрительные операции входа в систему по пользовательским и привилегированным (администраторы) удостоверениям по таким признакам, как атаки методом подбора, раскрытые учетные данные, попытки входа в систему из неизвестных расположений и зараженные устройства, чтобы обеспечить защиту в реальном времени. Что более важно, на основе этих подозрительных операций вычисляется серьезность риска пользователя, и можно настроить политики на основе рисков, чтобы обеспечить автоматическую защиту удостоверений своей организации. Дополнительные сведения см. в статье [Защита идентификации Azure Active Directory](active-directory-identityprotection.md).

В этом разделе показано, как включить защиту идентификации Azure Active Directory.

## <a name="steps-to-enable-azure-active-directory-identity-protection"></a>Инструкции по включению защиты идентификации Azure Active Directory
1. [Войдите](https://ms.portal.azure.com/) на портал Azure с правами глобального администратора. 
2. На портале Azure щелкните **Marketplace**.
   
    ![Создать](./media/active-directory-identityprotection-enable/01.png "Create")
3. В списке приложений щелкните **Безопасность+идентификация**.
   
    ![Создать](./media/active-directory-identityprotection-enable/02.png "Create")
4. Щелкните **Azure AD Identity Protection**(Защита идентификации Azure AD).
   
    ![Создание](./media/active-directory-identityprotection-enable/03.png "Create")
5. В колонке **Azure AD Identity Protection** (Защита идентификации Azure AD) щелкните **Создать**.
   
    ![Создание](./media/active-directory-identityprotection-enable/04.png "Create")

## <a name="next-steps"></a>Дальнейшие действия
* [Защита идентификации Azure Active Directory.](active-directory-identityprotection.md)




<!--HONumber=Nov16_HO3-->


