---
title: Учебник. Интеграция Azure Active Directory с Marketo | Документация Майкрософт
description: Узнайте, как настроить единый вход в Marketo через Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 48aee4565548fd57ef9925a41299b06660df2718
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Руководство. Интеграция Azure Active Directory с Marketo

В этом руководстве описано, как интегрировать Marketo с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Marketo обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Marketo.
- Вы можете включить автоматический вход пользователей в Marketo (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Marketo, вам потребуется:

- подписка Azure AD;
- подписка Marketo с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. добавление Marketo из коллекции,
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-marketo-from-the-gallery"></a>добавление Marketo из коллекции,
Чтобы настроить интеграцию Marketo с Azure AD, необходимо добавить Marketo из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Marketo из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Marketo**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_search.png)

5. На панели результатов выберите **Marketo** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Marketo для тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какому пользователю в Azure AD соответствует пользователь в Marketo. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Marketo.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Marketo.

Чтобы настроить и проверить единый вход Azure AD в Marketo, вам нужно выполнить следующие стандартные действия.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Marketo](#creating-a-marketo-test-user)** требуется для создания в Marketo пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Marketo.

**Чтобы настроить единый вход Azure AD в Marketo, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Marketo** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_samlbase.png)

3. В разделе **Домены и URL-адреса Marketo** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://saml.marketo.com/sp`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://login.marketo.com/saml/assertion/\<munchkinid\>`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки Marketo](http://investors.marketo.com/contactus.cfm).
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Marketo** щелкните **Настроить Marketo**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_configure.png) 

7. Чтобы получить идентификатор Munchkin для приложения, войдите в Marketo, используя учетные данные администратора, и выполните следующие действия.
   
    a. Войдите в приложение Marketo, используя учетные данные администратора.
   
    Б. Нажмите кнопку **Администратор** в области навигации вверху.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Перейдите в меню "Интеграция" и щелкните **ссылку Munchkin**.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. Скопируйте идентификатор Munchkin, показанный на экране, и введите URL-адрес ответа в мастере настройки Azure AD.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png) 

8. Для настройки единого входа в приложение выполните следующие действия:
   
    a. Войдите в приложение Marketo, используя учетные данные администратора.
   
    Б. Нажмите кнопку **Администратор** в области навигации вверху.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Перейдите в меню "Интеграция" и щелкните **Единый вход**.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Чтобы включить параметры SAML, нажмите кнопку **Изменить**.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    д. **Включите** параметры единого входа.
   
    f. Вставьте **идентификатор сущности SAML** в текстовое поле **Идентификатор издателя**.
   
    ж. В текстовом поле **Идентификатор сущности** введите URL-адрес в формате `http://saml.marketo.com/sp`.
   
    h. Укажите значение **В элементе идентификатора имени** для параметра "Расположение идентификатора пользователя".
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Если идентификатор пользователя имеет значение, отличное от имени участника-пользователя, измените это значение на вкладке Attribute (Атрибут).
   
    i. Отправьте сертификат, скачанный из мастера настройки Azure AD. **Сохраните** параметры.
   
    j. Измените параметры перенаправления страниц.
   
    k. Вставьте **URL-адрес службы единого входа SAML** в текстовое поле **URL-адрес входа**.
   
    l. Вставьте **URL-адрес выхода** в текстовое поле **URL-адрес выхода**.
   
    m. В поле **URL-адрес ошибки** вставьте **URL-адрес экземпляра Marketo** и нажмите кнопку **Сохранить**, чтобы сохранить параметры.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

9. Чтобы включить единый вход для пользователей, выполните следующие действия.
   
    a. Войдите в приложение Marketo, используя учетные данные администратора.
   
    Б. Нажмите кнопку **Администратор** в области навигации вверху.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. В меню **Безопасность** щелкните **Параметры входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. Установите флажок **Требовать единый вход** и **сохраните** параметры.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-marketo-test-user"></a>Создание тестового пользователя Marketo

В этом разделе описано, как создать пользователя Britta Simon в приложении Marketo. Для создания пользователя на платформе Marketo выполните следующие действия.

1. Войдите в приложение Marketo, используя учетные данные администратора.

2. Нажмите кнопку **Администратор** в области навигации вверху.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

3. Откройте меню **Безопасность** и выберите **Пользователи и роли**.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  

4. На вкладке "Пользователи" щелкните **Пригласить нового пользователя**.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 

5. Введите следующую информацию в мастере приглашения нового пользователя.
   
    a. Введите адрес **электронной почты** пользователя в соответствующее текстовое поле.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    Б. Введите **имя** .
   
    c. Введите **фамилию**.
   
    d. Щелкните **Далее**

6. На вкладке **Разрешения** выберите **роли пользователя** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. Нажмите кнопку **Отправить**, чтобы отправить приглашение пользователю.
   
    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. Пользователь получит уведомление по электронной почте. Ему нужно будет перейти по ссылке, чтобы изменить пароль и активировать учетную запись. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к Marketo.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Marketo, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Marketo**.

    ![Настройка единого входа](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Marketo на панели доступа, вы автоматически войдете в приложение Marketo.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png

