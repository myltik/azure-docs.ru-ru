---
title: "Перенос классических политик на портале Azure | Документация Майкрософт"
description: "Перенос классических политик на портале Azure."
services: active-directory
keywords: "условный доступ к приложениям, условный доступ посредством Azure Active Directory, безопасный доступ к ресурсам организации, политики условного доступа"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c584eddb5542c2c49d08d35bcaf8e7acb5c5b83a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Перенос классических политик на портале Azure 


С помощью [условного доступа](active-directory-conditional-access-azure-portal.md) Azure Active Directory (Azure AD) можно контролировать доступ авторизованных пользователей к облачным приложениям. Хотя назначение этой функции осталось без изменений, в новом выпуске портала Azure возможности условного доступа значительно расширены с помощью усовершенствований. Политики условного доступа, настроенные вне портала Azure, могут сосуществовать с новыми политиками, создаваемыми на портале Azure. При условии, что вы не отключили и не удалили их, эти политики по-прежнему применяются в вашей среде. Тем не менее, мы рекомендуем перенести классические политики в новые политики условного доступа Azure AD, так как:

- Новые политики позволяют реализовать сценарии, невозможные при использовании классических политик.

- Можно уменьшить количество политик, которыми необходимо управлять, объединяя их.   

Этот раздел поможет перенести существующие классические политики в новые политики условного доступа Azure AD.


## <a name="classic-policies"></a>Классические политики

Политики условного доступа для Azure AD и Intune, которые были созданы не на портале Azure, также называются **классическими политиками**. Чтобы перенести классические политики, не требуется доступ к классическому порталу Azure. На портале Azure доступно представление [**Classic policies (preview)** (Классические политики (предварительная версия))](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies), позволяющее просмотреть классические политики.

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>Открытие классической политики

**Вот как можно открыть классическую политику.**

1. На [портале Azure](https://portal.azure.com) на панели навигации слева щелкните **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. На странице **Azure Active Directory** в разделе **Управление** щелкните **Условный доступ**.

    ![Условный доступ](./media/active-directory-conditional-access-migration/02.png)
 
2. На странице **Условный доступ — политики** в разделе **Управление** щелкните **Classic policies (preview)** (Классические политики (предварительная версия)).

3. Из списка выберите классические политики, которые вас интересуют.   

    ![Условный доступ](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Политики условного доступа Azure AD

Этот раздел содержит подробные инструкции про переносу классических политик. Для их выполнения не требуется ознакомление с политиками условного доступа Azure AD. Тем не менее знание основных понятий и терминов условного доступа Azure AD поможет при переносе.

См.:

- [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md): получите дополнительные сведения об основных понятиях и терминология.

- [Начало работы с условным доступом в Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md): ознакомьтесь с пользовательским интерфейсом на портале Azure.


 





## <a name="multi-factor-authentication-policy"></a>Политика Многофакторной идентификации 

В этом примере показано, как перенести классическую политику, которая требует применения Многофакторной идентификации** для облачного приложения. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**Вот как можно перенести классическую политику.**

1. [Откройте классическую политику](#open-a-classic-policy) для получения параметров конфигурации.
2. Создайте политику условного доступа Azure AD, чтобы заменить классическую политику. 


### <a name="create-a-new-conditional-access-policy"></a>Создание политики условного доступа


1. На [портале Azure](https://portal.azure.com) на панели навигации слева щелкните **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. На странице **Azure Active Directory** в разделе **Управление** щелкните **Условный доступ**.

    ![Условный доступ](./media/active-directory-conditional-access-migration/02.png)



3. На странице **Условный доступ** на панели инструментов сверху нажмите кнопку **Добавить**, чтобы открыть страницу **Создать**.

    ![Условный доступ](./media/active-directory-conditional-access-migration/03.png)

4. На странице **Создать** в текстовом поле **Имя** введите имя политики.

    ![Условный доступ](./media/active-directory-conditional-access-migration/29.png)

5. В разделе **Назначения** щелкните **Пользователи и группы**.

    ![Условный доступ](./media/active-directory-conditional-access-migration/05.png)

    а. Если вы выбрали всех пользователей в классической политике, щелкните **Все пользователи**. 

    ![Условный доступ](./media/active-directory-conditional-access-migration/35.png)

    b. Если вы выбрали группы в классической политике, щелкните **Выбрать пользователей и группы**, а затем выберите необходимых пользователей и группы.

    ![Условный доступ](./media/active-directory-conditional-access-migration/36.png)

    c. Если вы исключили какие-либо группы, щелкните вкладку **Исключить** и выберите необходимых пользователей и группы. 

    ![Условный доступ](./media/active-directory-conditional-access-migration/37.png)

6. На странице **Создать** в разделе **Назначения** щелкните **Облачные приложения**, чтобы открыть страницу **Облачные приложения**.

    ![Условный доступ](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. На странице **Облачные приложения** выполните следующие действия.

    ![Условный доступ](./media/active-directory-conditional-access-migration/08.png)

    а. Щелкните **Выбрать приложения**.

    b. Нажмите кнопку **Выбрать**.

    c. На странице **Выбор** выберите облачное приложение и нажмите кнопку **Выбрать**.

    d. На странице **Облачные приложения** нажмите кнопку **Готово**.



9. Если выбран параметр **Требовать многофакторную проверку подлинности**:

    ![Условный доступ](./media/active-directory-conditional-access-migration/26.png)

    а. В разделе **Элементы управления доступом** щелкните **Предоставить**.

    ![Условный доступ](./media/active-directory-conditional-access-migration/27.png)

    b. На странице **Предоставление** щелкните **Разрешить доступ**, а затем щелкните **Требовать многофакторную проверку подлинности**.

    c. Нажмите кнопку **Выбрать**.


10. Щелкните **Включить**, чтобы включить политику.

    ![Условный доступ](./media/active-directory-conditional-access-migration/30.png)

11. Отключите классическую политику. 

    ![Условный доступ](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>Дальнейшие действия

- Если вы хотите узнать, как настроить политику условного доступа, см. статью о том, как [начать работу с условным доступом в Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Если вы готовы к настройке политик условного доступа для своей среды, см. статью [Рекомендации по работе с условным доступом в Azure Active Directory](active-directory-conditional-access-best-practices.md). 
