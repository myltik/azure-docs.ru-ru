---
title: "Руководство. Интеграция Azure Active Directory с EasyTerritory | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и EasyTerritory."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d29b362d-e986-4f67-8ff2-e158e49353aa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 64131f85cceb463bdd91cec40c2f272ece773691
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-easyterritory"></a>Руководство. Интеграция Azure Active Directory с EasyTerritory

В этом руководстве описано, как интегрировать EasyTerritory с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением EasyTerritory обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к EasyTerritory.
- Вы можете включить автоматический вход пользователей в EasyTerritory (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением EasyTerritory, вам потребуется:

- подписка Azure AD;
- подписка EasyTerritory с поддержкой единого входа (SSO).

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
>
>

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, то вы можете получить пробную версию [на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление приложения EasyTerritory из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-easyterritory-from-the-gallery"></a>Добавление приложения EasyTerritory из коллекции
Чтобы настроить интеграцию EasyTerritory с Azure AD, необходимо добавить EasyTerritory из коллекции в список управляемых приложений SaaS.

**Чтобы добавить EasyTerritory из коллекции, сделайте следующее:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **EasyTerritory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_001.png)

5. На панели результатов выберите **EasyTerritory** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описано, как настроить и проверить единый вход Azure AD в EasyTerritory с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в EasyTerritory соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в EasyTerritory.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в EasyTerritory.

Чтобы настроить и проверить единый вход Azure AD в EasyTerritory, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя EasyTerritory](#creating-an-easyterritory-test-user)** требуется для создания в EasyTerritory пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении EasyTerritory.

**Чтобы настроить единый вход Azure AD в EasyTerritory, выполните следующие действия:**

1. На портале управления Azure на странице интеграции с приложением **EasyTerritory** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_01.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения EasyTerritory** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_02.png)
   1. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://apps.easyterritory.com/<tenant id>/DEV/`
   2. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://apps.easyterritory.com/<tenant id>/DEV/AuthServices/Acs`.
    
4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то в разделе **Домены и URL-адреса приложения EasyTerritory** сделайте следующее:
    
    ![Настройка единого входа](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_03.png)
  1. Установите флажок **Показать дополнительные параметры URL-адресов**.
  2. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.easyterritory.com/`.

    >[!NOTE] 
    >Значения, указанные выше, приведены в качестве примера. Следует заменить их фактическими значениями URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти сведения, обратитесь в [службу поддержки EasyTerritory](mailto:sales@easyterritory.com).

5. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Настройка единого входа](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_04.png)     

6. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите **дату окончания срока действия**. Затем нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_300.png)

7. В разделе **Сертификат подписи SAML** выберите **Make new certificate active** (Сделать новый сертификат активным) и нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_05.png)

8. Во всплывающем окне **Rollover certificate** (Сертификат восстановления) нажмите кнопку **ОК**.

    ![Настройка единого входа](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_400.png)

9. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_06.png) 

10. Чтобы настроить единый вход для своего приложения, обратитесь в [службу поддержки EasyTerritory](mailto:sales@easyterritory.com) и предоставьте скачанный **файл метаданных**. 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_04.png) 
 1. В текстовом поле **Имя** введите **BrittaSimon**.
 2. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.
 3. Выберите **Показать пароль** и запишите значение поля **Пароль**.
 4. Щелкните **Создать**. 

### <a name="create-an-easyterritory-test-user"></a>Создание тестового пользователя в EasyTerritory

В этом разделе описано, как создать пользователя Britta Simon в приложении EasyTerritory. Обратитесь в [службу поддержки EasyTerritory](mailto:sales@easyterritory.com), чтобы добавить пользователей на платформу EasyTerritory.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к EasyTerritory.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в EasyTerritory, сделайте следующее:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **EasyTerritory**.

    ![Настройка единого входа](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент EasyTerritory на панели доступа, вы автоматически войдете в приложение EasyTerritory.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_203.png
