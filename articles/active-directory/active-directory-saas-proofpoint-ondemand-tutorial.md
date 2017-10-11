---
title: "Руководство по интеграции Azure Active Directory с Proofpoint on Demand | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в Proofpoint on Demand."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: b4c8d8c187fc865a905016f04a41843894249f5e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Руководство. Интеграция Azure Active Directory с Proofpoint on Demand

В этом руководстве описано, как интегрировать Proofpoint on Demand с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Proofpoint on Demand обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Proofpoint on Demand.
- Вы можете включить автоматический вход пользователей в Proofpoint on Demand (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Proofpoint on Demand, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Proofpoint on Demand.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Proofpoint on Demand из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-proofpoint-on-demand-from-the-gallery"></a>Добавление Proofpoint on Demand из коллекции
Чтобы настроить интеграцию приложения Proofpoint on Demand с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Proofpoint on Demand из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Proofpoint on Demand**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_search.png)

5. В области результатов выберите **Proofpoint on Demand** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Этот раздел описывает, как настроить и проверить единый вход Azure AD в Proofpoint on Demand с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в Proofpoint on Demand соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Proofpoint on Demand.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Proofpoint on Demand.

Чтобы настроить и проверить единый вход Azure AD в Proofpoint on Demand, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Proofpoint on Demand](#creating-a-proofpoint-on-demand-test-user)** требуется для создания пользователя Britta Simon в Proofpoint on Demand, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

Этот раздел описывает, как включить единый вход Azure AD на портале Azure и настроить его в приложении Proofpoint on Demand.

**Чтобы настроить единый вход Azure AD в Proofpoint on Demand, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Proofpoint on Demand** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
  
    ![Настройка единого входа](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Proofpoint on Demand** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<hostname>.pphosted.com/ppssamlsp`

    c.  В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их на фактические значения идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Proofpoint on Demand](https://www.proofpoint.com/us/support-services). 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_400.png)
    
6. В разделе **Конфигурация Proofpoint on Demand** щелкните **Настроить Proofpoint on Demand**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_configure.png) 

7. Чтобы настроить единый вход на стороне **Proofpoint on Demand**, нужно отправить скачанный **сертификат (Base64)**, **идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** [группе поддержки клиентов Proofpoint on Demand](https://www.proofpoint.com/us/support-services).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_01.png) 

2. Эти значения приведены в качестве примера. Замените эти значения фактическими значениями.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png) 

    А. В текстовом поле **Name** (Имя) введите **Britta Simon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи Britta Simon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-proofpoint-on-demand-test-user"></a>Создание тестового пользователя Proofpoint on Demand

В этом разделе описано, как создать пользователя Britta Simon в Proofpoint on Demand. Обратитесь в [службу поддержки клиентов Proofpoint on Demand](https://www.proofpoint.com/us/support-services), чтобы добавить пользователей на платформу Proofpoint on Demand.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Proofpoint on Demand.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Proofpoint on Demand, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Proofpoint on Demand**.

    ![Настройка единого входа](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку **Proofpoint on Demand** на панели доступа, вы автоматически войдете в приложение Proofpoint on Demand.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).  

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png

