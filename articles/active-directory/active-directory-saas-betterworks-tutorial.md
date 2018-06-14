---
title: Руководство по интеграции Azure Active Directory с BetterWorks | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и BetterWorks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5bb9505a-be02-46ae-9979-5308715d2b47
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 0d70e853450ac2ff5c16db005849239901184da9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347566"
---
# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Учебник. Интеграция Azure Active Directory с BetterWorks

В этом руководстве описано, как интегрировать BetterWorks с Azure Active Directory (Azure AD).

Интеграция Azure AD с BetterWorks обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к BetterWorks.
- Вы можете включить автоматический вход пользователей в BetterWorks (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с BetterWorks, вам потребуется:

- подписка Azure AD;
- подписка BetterWorks с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление BetterWorks из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-betterworks-from-the-gallery"></a>Добавление BetterWorks из коллекции
Чтобы настроить интеграцию BetterWorks с Azure AD, необходимо добавить BetterWorks из коллекции в список управляемых приложений SaaS.

**Чтобы добавить BetterWorks из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **BetterWorks**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_search.png)

5. На панели результатов выберите **BetterWorks** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в BetterWorks с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в BetterWorks соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BetterWorks.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в BetterWorks.

Чтобы настроить и проверить единый вход Azure AD в BetterWorks, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя BetterWorks](#creating-a-betterworks-test-user)** нужно для того, чтобы в BetterWorks также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении BetterWorks.

**Чтобы настроить единый вход Azure AD в BetterWorks, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **BetterWorks** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициируемом IdP**, то в разделе **Домены и URL-адреса приложения BetterWorks** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://app.betterworks.com/saml2/metadata/`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://app.betterworks.com/saml2/acs/`.

4. Если вы хотите настроить приложение в **режиме, инициируемом поставщиком услуг**, то в разделе **Домены и URL-адреса приложения BetterWorks** выполните следующие действия.
    
    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_url1.png)

    a. Установите флажок **Показать дополнительные параметры URL-адресов**.

    Б. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://app.betterworks.com`.

    > [!NOTE] 
    > Значения, указанные выше, приведены в качестве примера. Вместо них необходимо указать фактические значения URL-адреса ответа, идентификатора и URL-адреса для входа. Чтобы получить эти значения, обратитесь к [группе поддержки BetterWorks](mailto:support@betterworks.com).
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_certificate.png)  

5. Приложение BetterWorks ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке **Attribute** (Атрибут) приложения. На следующем снимке экрана приведен пример. 

    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_attribute.png)

6. В диалоговом окне **Атрибуты токена SAML** для каждой строки в таблице ниже выполните следующие действия:
 
   | Имя атрибута | Значение атрибута |
   | -------------- |  ------------ |
   | saml_token     | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

   a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_officespace_04.png)

    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_officespace_05.png)

   Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки. 

   c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
   d. Нажмите кнопку **ОК**.

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_general_400.png)

8. Чтобы настроить единый вход на стороне **BetterWorks**, отправьте скачанный **XML-файл метаданных** [группе поддержки BetterWorks](mailto:support@betterworks.com).


> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-betterworks-test-user"></a>Создание тестового пользователя BetterWorks

В этом разделе описано, как создать пользователя Britta Simon в приложении BetterWorks. Обратитесь к [группе поддержки BetterWorks](mailto:support@betterworks.com), чтобы добавить пользователей на платформу BetterWorks.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к BetterWorks.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в BetterWorks, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **BetterWorks**.

    ![Настройка единого входа](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент BetterWorks на панели доступа, вы автоматически войдете в приложение BetterWorks.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png

