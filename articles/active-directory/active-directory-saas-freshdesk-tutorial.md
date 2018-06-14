---
title: Руководство по интеграции Azure Active Directory с FreshDesk | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 76f91f865b2f2135e319d164a0823c57cda837c9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345339"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Руководство по интеграции Azure Active Directory с FreshDesk

В этом руководстве описано, как интегрировать приложение FreshDesk с Azure Active Directory (Azure AD).

Интеграция Azure AD с FreshDesk обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к FreshDesk.
- Вы можете включить автоматический вход пользователей в FreshDesk (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с FreshDesk, вам потребуется:

- подписка Azure AD;
- подписка FreshDesk с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление FreshDesk из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-freshdesk-from-the-gallery"></a>Добавление FreshDesk из коллекции
Чтобы настроить интеграцию FreshDesk с Azure AD, необходимо добавить FreshDesk из коллекции в список управляемых приложений SaaS.

**Чтобы добавить FreshDesk из коллекции, выполните следующие действия:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **FreshDesk**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. На панели результатов выберите **FreshDesk** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение FreshDesk с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь во FreshDesk соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в FreshDesk.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** во FreshDesk.

Чтобы настроить и проверить единый вход Azure AD в FreshDesk, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя FreshDesk](#creating-a-freshdesk-test-user)** требуется для создания во FreshDesk пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении FreshDesk.

**Чтобы настроить единый вход Azure AD во FreshDesk, выполните следующие действия:**

1. На портале управления Azure на странице интеграции с приложением **FreshDesk** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. В разделе **Домены и URL-адреса приложения FreshDesk** введите в поле **URL-адрес для входа** значение `https://<tenant-name>.freshdesk.com` или любое другое значение, предлагаемое Freshdesk.

    ![Настройка единого входа](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Обратите внимание, что это значение используется только в качестве примера. Вместо него необходимо указать фактический URL-адрес для входа. Для получения этого значения обратитесь в [службу поддержки клиентов FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg).  

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация FreshDesk** щелкните **Настроить FreshDesk**, чтобы открыть окно настройки единого входа. Скопируйте URL-адрес службы единого входа SAML и URL-адрес выхода из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. В другом окне браузера войдите на свой корпоративный веб-сайт Freshdesk в качестве администратора.

8. В верхнем меню щелкните **Администратор**.
   
   ![Администратор](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Администратор")

9. На вкладке **General Settings** (Общие параметры) щелкните **Security** (Безопасность).
   
   ![Безопасность](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Безопасность")

10. В разделе **Security** (Безопасность) выполните следующие действия.
   
    ![Единый вход](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Единый вход")
   
    a. Выберите для параметра **Single Sign On (SSO)** (Единый вход) значение **On** (Включено).

    Б. Выберите **Единый вход SAML**.

    c. Введите **URL-адрес службы единого входа SAML**, скопированный на портале Azure, в текстовое поле **SAML Login URL** (URL-адрес входа SAML).

    d. Введите **URL-адрес выхода**, скопированный на портале Azure, в текстовое поле **Logout URL** (URL-адрес выхода).

    д. Скопируйте значение поля **Thumbprint** (Отпечаток) из скачанного с портала Azure сертификата и вставьте его в текстовое поле **Security Certificate Fingerprint** (Отпечаток сертификата безопасности).  
 
    >[!TIP]
    >Дополнительные сведения см. в статье [Практическое руководство. Извлечение отпечатка сертификата](http://youtu.be/YKQF266SAxI). 
    
    f. Выберите команду **Сохранить**.


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-freshdesk-test-user"></a>Создание тестового пользователя FreshDesk

Чтобы пользователи Azure AD могли входить во FreshDesk, их необходимо подготовить во FreshDesk.  
В случае с FreshDesk подготовка выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Выполните вход в клиент **Freshdesk** .
2. В верхнем меню щелкните **Администратор**.
   
   ![Администратор](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Администратор")

3. На вкладке **General Settings** (Общие параметры) выберите **Agents** (Агенты).
   
   ![Агенты](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Агенты")

4. Нажмите **Создать агента**.
   
    ![Создание агента](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Создание агента")

5. В диалоговом окне "Сведения об агенте " выполните следующие действия.
   
   ![Сведения об агенте](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Сведения об агенте")
   
   a. В текстовое поле **Полное имя** введите имя учетной записи Azure AD, которую желаете подготовить.

   Б. В текстовое поле **Электронная почта** введите адрес электронной почты той учетной записи Azure AD, которую вы хотите подготовить.

   c. В текстовое поле **Название** введите название учетной записи Azure AD, которую желаете подготовить.

   d. Выберите **Agents role** (Роль агента) и нажмите кнопку **Assign** (Назначить).
       
   д. Выберите команду **Сохранить**.     
   
    >[!NOTE]
    >Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи. 
    > 
    
    >[!NOTE]
    >Вы можете использовать любые другие средства создания учетной записи пользователя Freshdesk или API, предоставляемые Freshdesk, для подготовки учетных записей пользователей AAD. во FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Box.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon во FreshDesk, выполните следующие действия:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **FreshDesk**.

    ![Настройка единого входа](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент FreshDesk на панели доступа, вы автоматически войдете в приложение FreshDesk.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png

