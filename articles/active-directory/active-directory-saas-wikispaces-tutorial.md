---
title: "Руководство по интеграции Azure Active Directory с Wikispaces | Документация Майкрософт"
description: "Узнайте, как использовать Wikispaces вместе с Azure Active Directory для настройки единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 665b95aa-f7f5-4406-9e2a-6fc299a1599c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e7761d07beebbbc68db7d3e30a26153bd8f9655


---
# <a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Руководство. Интеграция Azure Active Directory с Wikispaces
Цель данного учебника — показать интеграцию Azure и Wikispaces.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка Wikispaces с поддержкой единого входа.

После прохождения этого учебника пользователи Azure AD, назначенные Wikispaces, будут иметь возможность единого входа в приложение на веб-сайте компании Wikispaces (вход, инициированный поставщиком услуг) или входа с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Wikispaces
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

## <a name="enabling-the-application-integration-for-wikispaces"></a>Включение интеграции приложений для Wikispaces
В этом разделе показано, как включить интеграцию приложений для Wikispaces.

### <a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Wikispaces, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Wikispaces**.
   
   ![Коллекция приложений](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Application Gallery")
7. В области результатов выберите **Wikispaces** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Wikispaces со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Wikispaces** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Wikispaces?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Wikispaces** введите свой URL-адрес в формате *http://company.wikispaces.net*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configure App URL")
4. На странице **Настройка единого входа в Wikispaces** щелкните **Скачать метаданные**, а затем сохраните файл метаданных на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configure Single Sign-On")
5. Отправьте файл метаданных в группу поддержки Wikispaces.
   
   > [!NOTE]
   > Настройка единого входа должна выполняться группой поддержки Wikispaces. Сразу же после завершения настройки вы получите уведомление.
   > 
   > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в Wikispaces, они должны быть подготовлены для работы с Wikispaces.  
В случае использования Wikispaces подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Выполните вход на сайт компании **Wikispaces** в качестве администратора.
2. Перейдите в раздел **Участники**.
   
   ![Участники](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Members")
3. Щелкните **Пригласить пользователей**.
   
   ![Пригласить пользователей](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Invite People")
4. В разделе **Пригласить пользователей** выполните следующие действия.
   
   ![Пригласить пользователей](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Invite People")
   
   1. Введите **имя пользователя или электронный адрес** для действующей учетной записи AAD, которую вы хотите подготовить, в соответствующие текстовые поля.
   2. Нажмите кнопку **Отправить**.  
      
      > [!NOTE]
      > Владелец учетной записи Azure Active Directory получит электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.
      > 
      > 

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Wikispaces или API-интерфейсы, предоставляемые Wikispaces, для подготовки учетных записей пользователей AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которым нужно разрешить работу с приложением, назначив их.

### <a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>Чтобы назначить пользователей Wikispaces, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Wikispaces** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


