---
title: Руководство по интеграции Azure Active Directory с Showpad | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Showpad.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 48b6bee0-dbc5-4863-964d-75b25e517741
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: af0ed1c282ecc1262a9edf5b74dd08c245eb6806
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348695"
---
# <a name="tutorial-azure-active-directory-integration-with-showpad"></a>Руководство. Интеграция Azure Active Directory с Showpad

В этом руководстве описано, как интегрировать Showpad с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Showpad обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Showpad.
- Вы можете включить автоматический вход пользователей в Showpad (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Showpad, вам потребуется:

- подписка Azure AD;
- подписка Showpad с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Showpad из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-showpad-from-the-gallery"></a>Добавление Showpad из коллекции

Чтобы настроить интеграцию Showpad с Azure AD, необходимо добавить Showpad из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Showpad из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Showpad**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_search.png)

5. На панели результатов выберите **Showpad** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.

В этом разделе описана настройка и проверка единого входа Azure AD в Showpad с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Showpad соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Showpad.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Showpad.

Чтобы настроить и проверить единый вход Azure AD в Showpad, выполните описанные ниже процедуры.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Showpad](#creating-a-showpad-test-user)** требуется для того, чтобы в Showpad существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Showpad.

**Чтобы настроить единый вход Azure AD в Showpad, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Showpad** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Showpad** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<comapany-name>.showpad.biz/login`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company-name>.showpad.biz`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки Showpad](https://help.showpad.com). 
 


4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-showpad-tutorial/tutorial_general_400.png)

6. Войдите в клиент Showpad как администратор.

7. В верхнем меню щелкните пункт **Параметры**.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_001.png) 

8. Перейдите к параметру **Single Sign-On** (Единый вход) и щелкните **Enable** (Включить).
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_002.png)

9. В диалоговом окне **Add a SAML 2.0 Service** (Добавление службы SAML 2.0) выполните следующие действия.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_003.png) 
   
    a. В текстовом поле **Name** (Имя) введите имя поставщика идентификаторов (например, название своей компании).
   
    Б. Для параметра **Metadata Source** (Источник метаданных) выберите значение **XML**.
   
    c. Скопируйте содержимое XML-файла метаданных, скачанного с портала Azure, и вставьте его в текстовое поле **Metadata XML** (XML метаданных).
   
    d. Выберите параметр **Автоматическая подготовка учетных записей для новых пользователей при первом входе**.
   
    д. Нажмите кнопку **Submit**(Отправить).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-showpad-test-user"></a>Создание тестового пользователя Showpad

Цель этого раздела — создать в Showpad пользователя с именем Britta Simon. 

Приложение Showpad поддерживает JIT-подготовку. Вы уже включили эту функцию на этапе **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)**. 

В этом разделе никакие действия с вашей стороны не требуются. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Showpad.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Showpad, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Showpad**.

    ![Настройка единого входа](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Showpad" на панели доступа, вы автоматически войдете в приложение Showpad.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_203.png

