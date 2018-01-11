---
title: "Руководство по интеграции Azure Active Directory с Pluralsight | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Pluralsight."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c3f07d2-4e1f-4ea3-9025-c663f1f2b7b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: jeedes
ms.openlocfilehash: 62d148d78d9f98b6a3ddf1259177936b3976aeab
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Учебник. Интеграция Azure Active Directory с Pluralsight

В этом руководстве описано, как интегрировать Pluralsight с Azure Active Directory (Azure AD).

Интеграция Pluralsight с Azure AD обеспечивает приведенные далее преимущества.

- Можно управлять в Azure AD, который имеет доступ к Pluralsight.
- Позволяет пользователям автоматически получить вошедшего в Pluralsight (Single Sign-On) с помощью своих учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с Pluralsight, вам потребуется:

- подписка Azure AD;
- подписка Pluralsight с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Pluralsight из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-pluralsight-from-the-gallery"></a>Добавление Pluralsight из коллекции
Чтобы настроить интеграцию Pluralsight с Azure AD, необходимо добавить Pluralsight из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Pluralsight из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Pluralsight**выберите **Pluralsight** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![Pluralsight в списке результатов](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Pluralsight с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Pluralsight соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Pluralsight.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Pluralsight.

Чтобы настроить и проверить единый вход Azure AD в Pluralsight, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Pluralsight](#create-a-pluralsight-test-user)**  — на аналог Саймон Britta в Pluralsight, связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Pluralsight.

**Чтобы настроить единый вход Azure AD в Pluralsight, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Pluralsight** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_samlbase.png)

3. На **URL-адреса и домена Pluralsight** выполните следующие действия:

    ![URL-адреса и домена Pluralsight единого входа сведения](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<instancename>.pluralsight.com/sso/<companyname>`

    Б. В текстовом поле **Идентификатор** введите URL-адрес `www.pluralsight.com`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<instancename>.pluralsight.com/sp/ACS.saml2`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Обратитесь к [группа поддержки клиента Pluralsight](mailto:support@pluralsight.com) для получения этих значений. 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_certificate.png) 

5. В этом разделе описано, как включить единый вход Azure AD на портале Azure AD и настроить единый вход в приложении Pluralsight.

    Для приложения Pluralsight проверочные утверждения SAML должны иметь определенный формат. Для этого необходимо добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов маркера SAML. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_attribute.png)

    >[!NOTE]
    >Также можно добавить атрибут **Уникальный идентификатор** с соответствующим значением, таким как EmployeeID или другим, которое подходит для вашей организации. Обратите внимание, что это необязательный атрибут, однако его можно добавить для идентификации уникального пользователя. 

6. Чтобы добавить требуемые **атрибуты маркера SAML**, для каждой строки в таблице ниже выполните следующие действия.
   
   | Имя атрибута | Значение атрибута |
   | ---| --- |
   | Имя |user.givenname |
   | Фамилия |user.surname |
   | Email |user.mail |
   
   a. В нижней части страницы нажмите кнопку **add user attribute** to open the **Добавить атрибут пользователя** .
    
     ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_addattribute.png)
  
   Б. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.
  
   c. В списке **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.
  
   d. Нажмите кнопку **ОК**.    

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_400.png)

8. Чтобы настроить единый вход для своего приложения, свяжитесь с представителем отдела [профессиональных услуг](mailTo:professionalservices@pluralsight.com) Pluralsight и передайте ему скачанный файл метаданных.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-pluralsight-test-user"></a>Создание тестового пользователя Pluralsight

Цель этого раздела — создать пользователя с именем Britta Simon в Pluralsight. Чтобы добавить пользователей в учетную запись Pluralsight, обратитесь в [службу поддержки клиентов Pluralsight](mailto:support@pluralsight.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Pluralsight, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Pluralsight, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Pluralsight**.

    ![Pluralsight ссылку в список приложений](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Pluralsight на панели доступа, вы автоматически войдете в приложение Pluralsight.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png

