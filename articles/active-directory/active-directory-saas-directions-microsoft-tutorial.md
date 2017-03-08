---
title: "Учебник. Интеграция Azure Active Directory с Directions on Microsoft | Документация Майкрософт"
description: "Узнайте, как использовать Directions on Microsoft вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e0c8986f-2acd-418d-a306-437abc44b640
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: efea82512dad3f2e5d1c0dd45e9e2f6db9a283d4
ms.openlocfilehash: a5538e89b1c23e4a2ad45c5515de8167ef95b46a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Руководство. Интеграция Azure Active Directory с Directions on Microsoft
Цель данного учебника — показать интеграцию Azure Active Directory и Directions on Microsoft.  

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка Directions on Microsoft

Если у вас еще нет федеративной подписки Directions on Microsoft, направьте запрос по адресу **service@DirectionsOnMicrosoft.com**.

После изучения этого учебника пользователи Azure Active Directory, которые были назначены в Directions on Microsoft, смогут выполнять единый вход в приложение с помощью соответствующей функции.

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

* Включение интеграции приложений для Directions on Microsoft
* Настройка единого входа
* Настройка подготовки учетных записей пользователей
* Назначение пользователей

![Сценарий](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Сценарий")

## <a name="enable-the-application-integration-for-directions-on-microsoft"></a>Включение интеграции приложений для Directions on Microsoft
В этом разделе показано, как включить интеграцию приложений для Directions on Microsoft.

**Чтобы включить интеграцию приложений для Directions on Microsoft, сделайте следующее:**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **Directions on Microsoft**.
   
   ![Коллекция приложений](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Коллекция приложений")
7. В области результатов выберите **Directions on Microsoft**и нажмите кнопку **Завершить** , чтобы добавить приложение.
   
   ![Сценарий](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Сценарий")
   
## <a name="configure-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Directions on Microsoft со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

**Чтобы настроить единый вход, сделайте следующее:**

1. На странице интеграции с приложением **Directions on Microsoft** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Включение единого входа](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Включение единого входа")
2. На странице **Как пользователи должны входить в Directions on Microsoft?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Единый вход Microsoft Azure AD](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Единый вход Microsoft Azure AD")
3. На странице **Настроить URL-адрес приложения** в текстовом поле "URL-адрес для входа" введите **https://www.directionsonmicrosoft.com/user/login**, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Настройка URL-адреса приложения")
4. На странице **Настройка единого входа в Directions on Microsoft** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Настройка единого входа")
5. Отправьте файл метаданных службе поддержки Directions on Microsoft (*service@DirectionsOnMicrosoft.com*). Чтобы позволить службе поддержки Directions on Microsoft найти ваше членство федеративного веб-сайта, включите в сообщение электронной почты сведения о своей организации.
   
   >[!NOTE]
   >Единый вход Directions on Microsoft должна включить служба поддержки Directions on Microsoft. Вы получите уведомление, когда такой единый вход будет включен. 
   > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
  ![Настройка единого входа](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Настройка единого входа")
   
## <a name="configure-user-provisioning"></a>Настроить подготовку учетных записей пользователей

Элемент действия для настройки подготовки пользователей в Directions on Microsoft отсутствует.  

Когда назначенный пользователь пытается войти в Directions on Microsoft с помощью панели доступа, Directions on Microsoft проверяет, существует ли данный пользователь. Если учетная запись пользователя отсутствует, Directions on Microsoft автоматически создает ее.

## <a name="assign-users"></a>Назначить пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей Directions on Microsoft, сделайте следующее:**

1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Directions on Microsoft** нажмите кнопку **Назначить пользователей**.
   
   ![Назначение пользователей](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Назначение пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Да")


