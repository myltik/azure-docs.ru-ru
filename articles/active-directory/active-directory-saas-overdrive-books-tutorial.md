---
title: "Руководство по интеграции Azure Active Directory с Overdrive Books | Документация Майкрософт"
description: "Узнайте, как использовать Overdrive Books с Azure Active Directory для настройки единого входа, автоматической подготовки к работе и многого другого."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e68cede7-1130-4813-bd55-22a9a6fc6bf5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5d6f44f868010d50b36a9171b7db63aeee7e8bc0
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Учебник. Интеграция Azure Active Directory с Overdrive Books
Цель данного учебника — показать интеграцию Azure и Overdrive.  

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* подписка OverDrive с поддержкой единого входа (SSO).

После завершения этого руководства пользователи Azure AD, назначенные OverDrive, будут иметь возможность единого входа в приложение на веб-сайте компании OverDrive (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Overdrive
2. Настройка единого входа.
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Сценарий")

## <a name="enable-the-application-integration-for-overdrive"></a>Включение интеграции приложений для OverDrive
В этом разделе показано, как включить интеграцию приложений для Overdrive.

**Чтобы включить интеграцию приложений для OverDrive, выполните следующие действия:**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **OverDrive**.
   
   ![Коллекция приложений](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Коллекция приложений")
7. В области результатов выберите **OverDrive** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
   
## <a name="configure-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить аутентификацию в Overdrive со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

**Чтобы настроить единый вход, сделайте следующее:**

1. На странице интеграции с приложением **OverDrive** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Разрешить единый вход](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Разрешить единый вход")
2. На странице **Как пользователи должны входить в OverDrive?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Настройка единого входа")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в OverDrive** введите свой URL-адрес, используя шаблон *http://mslibrarytest.libraryreserve.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Настройка URL-адреса приложения")
4. На странице **Настройка единого входа в OverDrive** скачайте файл метаданных и отправьте его группе поддержки OverDrive.
   
   ![Настройка единого входа](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Настройка единого входа")
   
   >[!NOTE]
   >Группа поддержки OverDrive настроит единый вход и отправит вам уведомление о завершении настройки.
   > 
   > 
5. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Настройка единого входа")
   
## <a name="configure-user-provisioning"></a>Настроить подготовку учетных записей пользователей

Действие для настройки подготовки пользователей в OverDrive отсутствует.  

Когда назначенный пользователь пытается войти в OverDrive, учетная запись OverDrive создается автоматически (при необходимости).

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя OverDrive или API, предоставляемые OverDrive для подготовки учетных записей пользователя AAD.
>
>
 
## <a name="assign-users"></a>Назначить пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей OverDrive, выполните следующие действия:**

1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **OverDrive** щелкните **Назначить пользователей**.
   
   ![Назначение пользователей](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Назначение пользователей")
   
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

