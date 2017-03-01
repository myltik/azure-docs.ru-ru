---
title: "Учебник. Интеграция Azure Active Directory с Lucidchart | Документация Майкрософт"
description: "Узнайте, как использовать Lucidchart вместе с Azure Active Directory для реализации единого входа, автоматической подготовки и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c9a026e68526ea7f5fc5695349d04c724a8acd9b
ms.openlocfilehash: 6bb2f0672983fa3f4cb81291ee12306f21b9cebf
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Руководство. Интеграция Azure Active Directory с Lucidchart
Цель данного руководства — показать интеграцию Azure и Lucidchart.  

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* подписка Lucidchart с поддержкой единого входа.

После завершения этого руководства пользователи Azure AD, назначенные Lucidchart, будут иметь возможность единого входа в приложение на веб-сайте компании Lucidchart (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Lucidchart
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Сценарий")

## <a name="enabling-the-application-integration-for-lucidchart"></a>Включение интеграции приложений для Lucidchart
В этом разделе показано, как включить интеграцию приложений для Lucidchart.

**Чтобы включить интеграцию с приложением Lucidchart, выполните следующее.**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **Lucidchart**.
   
   ![Коллекция приложений](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Коллекция приложений")
7. В области результатов выберите **Lucidchart** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
   
## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Lucidchart со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

**Чтобы настроить единый вход, выполните следующие действия:**

1. На странице интеграции с приложением **Lucidchart** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Настройка единого входа")
2. На странице **Как пользователи должны входить в Lucidchart?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Настройка единого входа")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Lucidchart** введите URL-адрес, используемый для входа в приложение Lucidchart (например, *https://chart2.office.lucidchart.com/saml/sso/azure*), и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Настройка URL-адреса приложения")
4. На странице **Настройка единого входа в Lucidchart** нажмите кнопку **Скачать метаданные**, а затем сохраните файл данных на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Настройка единого входа")
5. В другом окне веб-браузера зайдите на веб-сайт компании Lucidchart в качестве администратора.
6. В верхнем меню нажмите пункт **Группа**.
   
   ![Team](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team") (Команда)
7. Выберите **Application \> Manage SAML** (Приложение > Управление SAML).
   
   ![Manage SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Manage SAML") (Управление SAML)
8. На странице диалогового окна **Параметры проверки подлинности SAML** выполните следующие действия.
   
   1. Установите флажок **Enable SAML Authentication** (Включить аутентификацию SAML), а затем выберите **Optional** (Необязательно).

  ![SAML Authentication Settings](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML Authentication Settings") (Параметры аутентификации SAML)
   2. В текстовом поле **Domain** (Домен) введите свой домен и нажмите кнопку **Change Certificate** (Изменить сертификат).

  ![Change Certificate](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Change Certificate") (Изменить сертификат)
   3. Откройте скачанный файл метаданных, скопируйте его содержимое и вставьте его в текстовое поле **Отправка метаданных** .

  ![Upload Metadata](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Upload Metadata") (Передача метаданных)
   4. Установите флажок **Automatically Add new users to the team** (Автоматически добавлять новых пользователей в группу), а затем нажмите кнопку **Save changes** (Сохранить изменения).

  ![Сохранение изменений](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Сохранение изменений")
9. Выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
  ![Настройка единого входа](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Настройка единого входа")
   
## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Элемент действия для настройки подготовки пользователей в Lucidchart отсутствует.  Когда назначенный пользователь пытается войти в Lucidchart с помощью панели доступа, Lucidchart проверяет, существует ли данный пользователь.  

Если учетная запись пользователя отсутствует, Lucidchart автоматически создает ее.

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей в Lucidchart, выполните следующее.**

1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Lucidchart** щелкните **Назначить пользователей**.
   
   ![Назначение пользователей](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Назначение пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


