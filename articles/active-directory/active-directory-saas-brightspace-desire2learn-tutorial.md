---
title: "Руководство. Интеграция Azure Active Directory с Brightspace (разработка Desire2Learn) | Документация Майкрософт"
description: "Узнайте, как использовать Brightspace (разработка Desire2Learn) вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: cdd7a42f12c145327249ff7c03ede9a465374e35
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Руководство. Интеграция Azure Active Directory с Brightspace (разработка Desire2Learn)
Цель данного учебника — показать интеграцию Azure и Brightspace (разработка Desire2Learn).  

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* Подписка с поддержкой единого входа Brightspace (разработка Desire2Learn)

После завершения этого руководства пользователи Azure AD, назначенные Brightspace (разработка Desire2Learn), будут иметь возможность единого входа в приложение на веб-сайте компании Brightspace (разработка Desire2Learn; вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

* Включение интеграции приложений для Brightspace (разработка Desire2Learn)
* Настройка единого входа
* Настройка подготовки учетных записей пользователей
* Назначение пользователей

![Сценарий](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Сценарий")

## <a name="enable-the-application-integration-for-brightspace-by-desire2learn"></a>Включение интеграции приложений для Brightspace (разработка Desire2Learn)
В этом разделе показано, как включить интеграцию приложений для Brightspace (разработка Desire2Learn).

**Чтобы включить интеграцию приложений для Brightspace (разработка Desire2Learn), сделайте следующее:**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **Brightspace by Desire2Learn** (Brightspace (разработка Desire2Learn)).
   
   ![Коллекция приложений](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Коллекция приложений")
7. В области результатов выберите **Brightspace by Desire2Learn** (Brightspace (разработка Desire2Learn)) и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Brightspace (разработка Desire2Learn)](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace (разработка Desire2Learn)")
   
## <a name="configure-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Brightspace (разработка Desire2Learn) со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

**Чтобы настроить единый вход, выполните следующие действия:**

1. На странице интеграции с приложением **Brightspace by Desire2Learn** (Brightspace (разработка Desire2Learn)) классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Настройка единого входа")
2. На странице **How would you like users to sign on to Brightspace by Desire2Learn** (Как пользователи должны входить в Brightspace (разработка Desire2Learn)?) выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Настройка единого входа")
3. На странице **Настройка URL-адреса приложения** выполните следующие действия.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Настройка URL-адреса приложения")   
   1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в ваше приложение **Brightspace by Desire2Learn** (Brightspace (разработка Desire2Learn)) (например, *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
   2. Нажмите кнопку **Далее**.
4. На странице **Configure single sign-on at Brightspace by Desire2Learn** (Настройка единого входа в Brightspace (разработка Desire2Learn)) нажмите кнопку **Скачать метаданные**, чтобы скачать их, а затем сохраните файл данных локально на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Настройка единого входа")
5. Отправьте загруженный файл метаданных в службу поддержки Brightspace (разработка Desire2Learn).
   
   >[!NOTE]
   >Настройка единого входа должна выполняться службой поддержки Brightspace (разработка Desire2Learn).
   >Как только единый вход для вашей подписки будет включен, вы получите уведомление.
   > 
 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Настройка единого входа")
   
## <a name="configure-user-provisioning"></a>Настроить подготовку учетных записей пользователей

Чтобы пользователи Azure AD могли выполнить вход в Brightspace (разработка Desire2Learn), они должны быть подготовлены для Brightspace (разработка Desire2Learn).  

В случае Brightspace (разработка Desire2Learn) учетные записи пользователей должны создаваться службой поддержки Brightspace (разработка Desire2Learn).

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя Brightspace (разработка Desire2Learn) или API, предоставляемые Brightspace (разработка Desire2Learn) для подготовки учетных записей пользователя Azure Active Directory. 
> 

## <a name="assign-users"></a>Назначить пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей Brightspace (разработка Desire2Learn), сделайте следующее:**
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Brightspace by Desire2Learn** (Brightspace (разработка Desire2Learn)) нажмите кнопку **Назначить пользователей**.
   
  ![Назначение пользователей](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Назначение пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
  ![Да](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


