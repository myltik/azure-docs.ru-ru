---
title: "Руководство по интеграции Azure Active Directory с SimpleNexus | Документация Майкрософт"
description: "Узнайте, как использовать SimpleNexus вместе с Azure Active Directory для реализации единого входа, автоматической подготовки к работе и многого другого."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89821a05-88e2-4579-b144-0123b2b9cb95
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: da3b1e506e603b1f5e3660f31c6d939d77cd2938
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>Учебник. Интеграция Azure Active Directory с SimpleNexus
Цель данного учебника — показать интеграцию Azure и SimpleNexus.  

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* подписка SimpleNexus с поддержкой единого входа.

После завершения этого руководства пользователи Azure AD, назначенные SimpleNexus, будут иметь возможность единого входа в приложение на веб-сайте компании SimpleNexus (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для SimpleNexus 
2. Настройка единого входа. 
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Сценарий")

## <a name="enabling-the-application-integration-for-simplenexus"></a>Включение интеграции приложений для SimpleNexus 
В этом разделе показано, как включить интеграцию приложений для SimpleNexus.

**Чтобы включить интеграцию приложений для SimpleNexus, выполните следующее.**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Приложения")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавление приложения](./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Добавление приложения")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавление приложения из коллекции](./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Добавление приложения из коллекции")

6. В **поле поиска** введите **SimpleNexus**.
   
    ![Коллекция приложений](./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Коллекция приложений")

7. В области результатов выберите **SimpleNexus** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Simple Nexus](./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Simple Nexus")
   
## <a name="configure-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить аутентификацию в SimpleNexus со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

**Чтобы настроить единый вход, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **SimpleNexus** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Настройка единого входа")

2. На странице **Как пользователи должны входить в SimpleNexus?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Настройка единого входа")

3. На странице **Настроить URL-адрес приложения** введите свой URL-адрес в текстовом поле **URL-адрес входа в SimpleNexus**, используя шаблон *https://simplenexus.com/CompanyName\_login*, а затем нажмите кнопку **Далее**.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Настройка URL-адреса приложения")

4. На странице **Настройка единого входа в SimpleNexus** нажмите кнопку **Скачать метаданные**, а затем перешлите файл метаданных в группу поддержки SimpleNexus.
   
    ![Настройка единого входа](./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Настройка единого входа")
   
    >[!NOTE]
    >Единый вход должна включить группа поддержки SimpleNexus. 
    > 

5. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Настройка единого входа")
   
## <a name="configure-user-provisioning"></a>Настроить подготовку учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в SimpleNexus, они должны быть подготовлены для работы с SimpleNexus.

В случае SimpleNexus подготовка выполняется вручную администратором клиента.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователей SimpleNexus или API, предоставляемые SimpleNexus для подготовки учетных записей пользователей AAD. 
> 

## <a name="assign-users"></a>Назначить пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей SimpleNexus, выполните следующее.**

1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **SimpleNexus** нажмите кнопку **Назначить пользователей**.
   
    ![Назначение пользователей](./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Назначение пользователей")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


