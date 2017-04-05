---
title: "Руководство по интеграции Azure Active Directory с Thoughtworks Mingle | Документация Майкрософт"
description: "Узнайте, как использовать Thoughtworks Mingle вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 3a58f6d2d3bc39af650f5d62acdf09d769cd7ad0
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Учебник. Интеграция Azure Active Directory с Thoughtworks Mingle
Цель данного учебника — показать интеграцию Azure и Thoughtworks Mingle.  

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Thoughtworks Mingle

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Thoughtworks Mingle
2. Настройка единого входа.
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Сценарий")

## <a name="enable-the-application-integration-for-thoughtworks-mingle"></a>Включение интеграции приложений для Thoughtworks Mingle
В этом разделе показано, как включить интеграцию приложений для Thoughtworks Mingle.

**Чтобы включить интеграцию приложений для Thoughtworks Mingle, выполните следующее.**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Приложения")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавление приложения](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Добавление приложения")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавление приложения из коллекции](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Добавление приложения из коллекции")

6. В **поле поиска** введите **thoughtworks mingle**.
   
    ![Коллекция приложений](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Коллекция приложений")

7. В области результатов выберите **Thoughtworks Mingle** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![ThoughtWorks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

## <a name="configure-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Thoughtworks Mingle с помощью своей учетной записи Azure AD, используя федерацию на основе протокола SAML.  

В рамках этой процедуры потребуется отправить сертификат в Thoughtworks Mingle.

**Чтобы настроить единый вход, выполните следующие действия:**

1. На странице интеграции с приложением **Thoughtworks Mingle** на классическом портале Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Настройка единого входа")

2. На странице **Как пользователи будут входить в Thoughtworks Mingle** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Настройка единого входа")

3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес клиента Thoughtworks Mingle** введите свой URL-адрес в формате *http://company.mingle.thoughtworks.com*, а затем нажмите кнопку **Далее**.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Настройка URL-адреса приложения")

4. На странице **Настройка единого входа в Thoughtworks Mingle** нажмите кнопку "Загрузить метаданные", а затем сохраните эти данные локально на компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Настройка единого входа")

5. Войдите на корпоративный веб-сайт **Thoughtworks Mingle** в качестве администратора.

6. Откройте вкладку **Admin** (Администратор), а затем щелкните **SSO Config** (Конфигурация единого входа).
   
    ![Настройка единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "Настройка единого входа")

7. В разделе **Конфигурация единого входа** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "Настройка единого входа")   
 1. Чтобы отправить файл метаданных, нажмите кнопку **Выбрать файл**. 
 2. Нажмите кнопку **Сохранить изменения**.

8. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Настройка единого входа")

## <a name="configure-user-provisioning"></a>Настроить подготовку учетных записей пользователей
Чтобы пользователи AAD могли войти систему, они должны быть подготовлены для приложения Thoughtworks Mingle с использованием их имен пользователей Azure Active Directory.  

* В случае с Thoughtworks Mingle подготовка выполняется вручную.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Войдите на корпоративный веб-сайт Thoughtworks Mingle в качестве администратора.

2. Щелкните **Профиль**.
   
    ![Ваш первый проект](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Ваш первый проект")

3. Откройте вкладку **Admin** (Администратор), а затем щелкните **Users** (Пользователи).
   
    ![Пользователи](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Пользователи")

4. Щелкните **Новый пользователь**.
   
    ![Новый пользователь](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "Новый пользователь")

5. На странице диалогового окна **Новый пользователь** выполните следующие действия.
   
    ![Новый пользователь](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "Новый пользователь")   
  1. Введите в текстовые поля **Sign-in name** (Учетное имя), **Display name** (Отображаемое имя), **Choose password** (Выберите пароль) и **Confirm password** (Подтвердите пароль) соответствующие данные действующей учетной записи AAD, которую вы хотите подготовить. 
  2. В поле **User type** (Тип пользователя) выберите значение **Full user** (С полным доступом).
  3. Щелкните **Создать этот профиль**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя Thoughtworks Mingle или API-интерфейсы, предоставляемые Thoughtworks Mingle для подготовки учетных записей пользователей AAD.
>  

## <a name="assign-users"></a>Назначить пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей Thoughtworks Mingle, выполните следующее.**

1. На классическом портале Azure создайте тестовую учетную запись.

2. На странице интеграции с приложением **Thoughtworks Mingle** нажмите кнопку **Назначить пользователей**.
   
    ![Назначение пользователей](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Назначение пользователей")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


