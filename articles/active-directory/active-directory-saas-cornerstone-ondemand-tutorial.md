---
title: "Учебник. Интеграция Azure Active Directory с Cornerstone OnDemand | Документация Майкрософт"
description: "Узнайте, как использовать Cornerstone OnDemand вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bbbae7d7a5e9b0fbfe07bd3f2aa61df77b56c5df
ms.openlocfilehash: 7fce07464b90c81da7a015246d5757ecc768792a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Руководство. Интеграция Azure Active Directory с Cornerstone OnDemand
Цель данного учебника — показать интеграцию Azure и Cornerstone OnDemand.

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Cornerstone OnDemand

После завершения этого руководства пользователи Azure AD, назначенные Cornerstone OnDemand, будут иметь возможность единого входа в приложение на веб-сайте компании Cornerstone OnDemand (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

* Включение интеграции приложений для Cornerstone OnDemand
* Настройка единого входа
* Настройка подготовки учетных записей пользователей
* Назначение пользователей

![Сценарий](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Сценарий")

## <a name="enable-the-application-integration-for-cornerstone-ondemand"></a>Включение интеграции приложений для Cornerstone OnDemand
В этом разделе показано, как включить интеграцию приложений для Cornerstone OnDemand.

**Чтобы включить интеграцию приложений для Cornerstone OnDemand, сделайте следующее:**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **cornerstone ondemand**.
   
   ![Коллекция приложений](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Коллекция приложений")
7. В области результатов выберите **Cornerstone OnDemand** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
   
## <a name="configure-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Cornerstone OnDemand со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

**Чтобы настроить единый вход, сделайте следующее:**
1. На странице интеграции с приложением **Cornerstone OnDemand** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Включение единого входа](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Включение единого входа")
2. На странице **Как пользователи должны входить в Cornerstone OnDemand?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Единый вход Microsoft Azure AD](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Единый вход Microsoft Azure AD")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Cornerstone OnDemand** введите свой URL-адрес в формате *http://компания.csod.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Настройка URL-адреса приложения")
4. На странице **Настройка единого входа в Cornerstone OnDemand** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Настройка единого входа")
5. Отправьте следующие элементы в службу поддержки Cornerstone OnDemand.  
   1. Скачанный сертификат
   2. Значение **URL-адрес удаленного входа**
   3. Значение **URL-адрес удаленного выхода**

   >[!NOTE]
   >Единый вход должна настроить служба поддержки Cornerstone OnDemand. Сразу же после завершения настройки вы получите уведомление от службы поддержки.
   > 
6. Выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
  ![Настройка единого входа](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Настройка единого входа")
   
## <a name="configure-user-provisioning"></a>Настроить подготовку учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Cornerstone OnDemand, они должны быть подготовлены для работы с Cornerstone OnDemand. В случае с Cornerstone OnDemand подготовка выполняется вручную.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Отправьте сведения (например, имя, адрес электронной почты) о том пользователе Azure AD, которого необходимо подготовить, в службу поддержки Cornerstone OnDemand.

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя Cornerstone OnDemand или API, предоставляемые Cornerstone OnDemand, для подготовки учетных записей пользователей AAD. 
> 

## <a name="assign-users"></a>Назначить пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей Cornerstone OnDemand, сделайте следующее:**

1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Cornerstone OnDemand** нажмите кнопку **Назначить пользователей**.
   
   ![Назначение пользователей](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Назначение пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Назначение пользователей](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Назначение пользователей")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


