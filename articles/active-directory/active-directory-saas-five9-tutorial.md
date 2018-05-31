---
title: Руководство по интеграции Azure Active Directory с Five9 Plus Adapter (CTI, Contact Center Agents) | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Five9 Plus Adapter (CTI, Contact Center Agents).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: d5c6b2c658a899b3c4363803dc3858cc2b6bab46
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345437"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Руководство по интеграции Azure Active Directory с Five9 Plus Adapter (CTI, Contact Center Agents)

В этом учебнике описано, как интегрировать Five9 Plus Adapter (CTI, Contact Center Agents) с Azure Active Directory (Azure AD).

Интеграция Five9 Plus Adapter (CTI, Contact Center Agents) с Azure AD предоставляет следующие преимущества:

- Вы можете управлять доступом к Five9 Plus Adapter (CTI, Contact Center Agents) в Azure AD.
- Вы можете включить автоматический вход пользователей в Five9 Plus Adapter (CTI, Contact Center Agents) (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Five9 Plus Adapter (CTI, Contact Center Agents), вам потребуется:

- подписка Azure AD;
- подписка Five9 Plus Adapter (CTI, Contact Center Agents) с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Five9 Plus Adapter (CTI, Contact Center Agents) из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Добавление Five9 Plus Adapter (CTI, Contact Center Agents) из коллекции
Чтобы настроить интеграцию Five9 Plus Adapter (CTI, Contact Center Agents) с Azure AD, необходимо добавить Five9 Plus Adapter (CTI, Contact Center Agents) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Five9 Plus Adapter (CTI, Contact Center Agents) из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Five9 Plus Adapter (CTI, Contact Center Agents)**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-five9-tutorial/tutorial_five9_search.png)

5. На панели результатов выберите **Five9 Plus Adapter (CTI, Contact Center Agents)** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Five9 Plus Adapter (CTI, Contact Center Agents) для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Five9 Plus Adapter (CTI, Contact Center Agents) соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Five9 Plus Adapter (CTI, Contact Center Agents).

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Five9 Plus Adapter (CTI, Contact Center Agents).

Чтобы настроить и проверить единый вход Azure AD в Five9 Plus Adapter (CTI, Contact Center Agents), вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Five9 Plus Adapter (CTI, Contact Center Agents)](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)** требуется для того, чтобы в Five9 Plus Adapter (CTI, Contact Center Agents) существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Five9 Plus Adapter (CTI, Contact Center Agents).

**Чтобы настроить единый вход Azure AD для Five9 Plus Adapter (CTI, Contact Center Agents), выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Five9 Plus Adapter (CTI, Contact Center Agents)** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-five9-tutorial/tutorial_five9_samlbase.png)

3. В разделе **Домены и URL-адреса Five9 Plus Adapter (CTI, Contact Center Agents)** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-five9-tutorial/tutorial_five9_url.png)
    
    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    |    Среда      |       URL-адрес      |
    | :-- | :-- |
    | Для "Five9 Plus Adapter for Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Для "Five9 Plus Adapter for Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Для "Five9 Plus Adapter for Agent Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:

    |      Среда     |      URL-адрес      |
    | :--                  | :--           |
    | Для "Five9 Plus Adapter for Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Для "Five9 Plus Adapter for Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Для "Five9 Plus Adapter for Agent Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-five9-tutorial/tutorial_five9_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-five9-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Five9 Plus Adapter (CTI, Contact Center Agents)** щелкните **Настроить Five9 Plus Adapter (CTI, Contact Center Agents)**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-five9-tutorial/tutorial_five9_configure.png) 

7. Чтобы настроить единый вход на стороне **Five9 Plus Adapter (CTI, Contact Center Agents)**, нужно отправить скачанный **сертификат (Base64), URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** в [службу поддержки Five9 Plus Adapter (CTI, Contact Center Agents)](https://www.five9.com/about/contact). Для дальнейшей настройки единого входа выполните следующие действия в соответствии с адаптером:

    a. Руководство по администрированию "Five9 Plus Adapter for Agent Desktop Toolkit": [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    Б. Руководство по администрированию "Five9 Plus Adapter for Microsoft Dynamics CRM": [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Руководство по администрированию "Five9 Plus Adapter for Zendesk": [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Создание тестового пользователя Five9 Plus Adapter (CTI, Contact Center Agents)

В этом разделе вы создадите тестового пользователя по имени Britta Simon в Five9 Plus Adapter (CTI, Contact Center Agents). Чтобы добавить пользователей в Five9 Plus Adapter (CTI, Contact Center Agents), обратитесь в [службу поддержки Five9 Plus Adapter (CTI, Contact Center Agents)](https://www.five9.com/about/contact). Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Five9 Plus Adapter (CTI, Contact Center Agents).

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Five9 Plus Adapter (CTI, Contact Center Agents), выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Five9 Plus Adapter (CTI, Contact Center Agents)**.

    ![Настройка единого входа](./media/active-directory-saas-five9-tutorial/tutorial_five9_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Five9 Plus Adapter (CTI, Contact Center Agents)" на панели доступа, вы автоматически войдете в приложение Five9 Plus Adapter (CTI, Contact Center Agents).
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-five9-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-five9-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-five9-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-five9-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-five9-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-five9-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-five9-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-five9-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-five9-tutorial/tutorial_general_203.png

