---
title: Руководство по интеграции Azure Active Directory с BenefitHub | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и BenefitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4069fe32-a452-463f-973e-7aa0baa4c2fa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.openlocfilehash: bd5bcb73fbfffacf04ccbe9c592c1f821a165530
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34337832"
---
# <a name="tutorial-azure-active-directory-integration-with-benefithub"></a>Руководство по интеграции Azure Active Directory с BenefitHub

В этом руководстве описано, как интегрировать BenefitHub с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением BenefitHub обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к BenefitHub.
- Вы можете включить автоматический вход пользователей в BenefitHub (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с BenefitHub, вам потребуется:

- подписка Azure AD;
- подписка BenefitHub с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление BenefitHub из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-benefithub-from-the-gallery"></a>Добавление BenefitHub из коллекции
Чтобы настроить интеграцию BenefitHub с Azure AD, необходимо добавить BenefitHub из коллекции в список управляемых приложений SaaS.

**Чтобы добавить BenefitHub из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **BenefitHub**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_search.png)

5. На панели результатов выберите **BenefitHub** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в BenefitHub с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в BenefitHub соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BenefitHub.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в BenefitHub.

Чтобы настроить и проверить единый вход Azure AD в BenefitHub, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя BenefitHub](#creating-a-benefithub-test-user)** нужно для того, чтобы в BenefitHub также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале Azure и настроить его в приложении BenefitHub.

**Чтобы настроить единый вход Azure AD в BenefitHub, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **BenefitHub** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_samlbase.png)

3. В разделе **Домены и URL-адреса приложения BenefitHub** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_url1.png)
  
    a. В текстовом поле **Идентификатор** введите `urn:benefithub:passport`.
    
    Б. В текстовом поле **URL-адрес ответа** введите: `https://passport.benefithub.info/saml/post/ac`.

4. Приложение BenefitHub ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. 

    ![Настройка единого входа](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_attribute.png)

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | ------------------- | -------------------- |    
    | organizationid | < organizationid > |

    > [!NOTE]
    > Это значение атрибута приведено для примера. Введите вместо этого значения фактическое значение organizationid. Обратитесь к [группе поддержки BenefitHub](https://www.benefithub.com/Home/ContactUs) для получения фактического значения organizationid.
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-benefithub-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/active-directory-saas-benefithub-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

    > [!NOTE] 
    > Чтобы настроить утверждение SAML, обратитесь в [службу поддержки BenefitHub](https://www.benefithub.com/Home/ContactUs) и запросите значение атрибута уникального идентификатора для своего клиента. Это значение необходимо для настройки пользовательского утверждения для вашего приложения.

6. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-benefithub-tutorial/tutorial_general_400.png)

8. Чтобы настроить единый вход на стороне **BenefitHub**, отправьте скачанный **XML-файл метаданных** [группе поддержки BenefitHub](https://www.benefithub.com/Home/ContactUs). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benefithub-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benefithub-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benefithub-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benefithub-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-benefithub-test-user"></a>Создание тестового пользователя BenefitHub

В этом разделе описано, как создать пользователя Britta Simon в приложении BenefitHub. Обратитесь к [группе поддержки BenefitHub](https://www.benefithub.com/Home/ContactUs), чтобы добавить пользователей на платформу BenefitHub. Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к BenefitHub.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в BenefitHub, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **BenefitHub**.

    ![Настройка единого входа](./media/active-directory-saas-benefithub-tutorial/tutorial_benefithub_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "BenefitHub" на панели доступа, вы автоматически войдете в приложение BenefitHub.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-benefithub-tutorial/tutorial_general_203.png

