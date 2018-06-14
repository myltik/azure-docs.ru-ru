---
title: Руководство. Интеграция Azure Active Directory с MaxxPoint | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в MaxxPoint
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 15ba026e-96fc-4ae8-b135-0169da810e99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 9368abc5694fc52b8cad2d5cc9faafecf853004d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340688"
---
# <a name="tutorial-azure-active-directory-integration-with-maxxpoint"></a>Руководство. Интеграция Azure Active Directory с MaxxPoint

В этом руководстве описано, как интегрировать MaxxPoint с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением MaxxPoint предоставляет следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к MaxxPoint.
- Вы можете включить автоматический вход пользователей в MaxxPoint (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с MaxxPoint, вам потребуется:

- подписка Azure AD;
- подписка MaxxPoint с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление MaxxPoint из коллекции
2. настройка и проверка единого входа в Azure AD.


## <a name="adding-maxxpoint-from-the-gallery"></a>Добавление MaxxPoint из коллекции
Чтобы настроить интеграцию MaxxPoint с Azure AD, необходимо добавить MaxxPoint из коллекции в список управляемых приложений SaaS.

**Чтобы добавить MaxxPoint из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **MaxxPoint**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_001.png)

5. На панели результатов выберите **MaxxPoint** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в MaxxPoint с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в MaxxPoint соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MaxxPoint.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в MaxxPoint.

Чтобы настроить и проверить единый вход Azure AD в MaxxPoint, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя MaxxPoint](#creating-a-maxxpoint-test-user)** требуется для создания в MaxxPoint пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении MaxxPoint.

**Чтобы настроить единый вход Azure AD в MaxxPoint, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **MaxxPoint** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_300.png)

3. В разделе **Домены и URL-адреса приложения MaxxPoint** выполнять какие-либо действия не требуется, если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**.

    ![Настройка единого входа](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_02.png)
    
4. В разделе **Домены и URL-адреса приложения MaxxPoint** выполните следующие действия, если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**:
    
    ![Настройка единого входа](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_03.png)

    a. Выберите параметр **Показать дополнительные параметры URL-адресов**.

    Б. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://maxxpoint.westipc.com/default/sso/login/entity/<customer-id>-azure`.

    > [!NOTE] 
    > Обратите внимание, что это значение используется только в качестве примера. Вместо него необходимо указать фактический URL-адрес для входа. Чтобы получить это значение, позвоните в службу поддержки MaxxPoint по номеру **888-728-0950**.

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_06.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_400.png)

7. Чтобы настроить единый вход для приложения, позвоните в службу поддержки MaxxPoint по номеру **888-728-0950**. Вы получите дальнейшие указания по тому, как предоставить скачанный **XML-файл метаданных**. 

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-maxxpoint-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-maxxpoint-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-maxxpoint-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-maxxpoint-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**. 

### <a name="creating-a-maxxpoint-test-user"></a>Создание тестового пользователя MaxxPoint

В этом разделе описано, как создать пользователя Britta Simon в приложении MaxxPoint. Позвоните в службу поддержки MaxxPoint по номеру **888-728-0950**, чтобы добавить пользователей в приложение MaxxPoint.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к MaxxPoint.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в MaxxPoint, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **MaxxPoint**.

    ![Настройка единого входа](./media/active-directory-saas-maxxpoint-tutorial/tutorial_maxxpoint_50.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент MaxxPoint на панели доступа, вы автоматически войдете в приложение MaxxPoint.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-maxxpoint-tutorial/tutorial_general_203.png